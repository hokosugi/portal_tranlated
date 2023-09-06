# 再現可能なcanister ビルドの作成

## 概要

コンセンサスプロトコルのおかげで、Internet Computer は常にcanister のコードを正しく実行します。しかし、これはcanister に対して**正しい**コードを実行していることを意味するわけではありません。 誰かが開発したcanister を使用している場合、canister が本当に意図したコードを実行しているかどうかを、あなたのために重要な決定を行う制御を与える前に検証したいと思うかもしれません。たとえば、ICP を別のcanister に送るなどです。これを確認するには、2つの質問に答える必要があります：

1.  canister 、どのWebAssembly （Wasm）コードが実行されていますか？
2.  canisters は通常、Motoko や Rust などの高水準言語で書かれており、Wasm で直接書かれているわけではありません。次に、実行されているWasmは本当にソースコードをコンパイルした結果なのでしょうか？

このドキュメントの残りの部分は、これらの質問に答えるものです。最初の質問については、Internet Computer がcanister コードに関する情報をどのように提供しているかを見ていきます。2つ目の質問に答えられるようにするためには、canister 、作者はソースからWasmコードを信頼でき、[再現可能なビルドを](https://reproducible-builds.org/docs/definition/)保証する必要があります。このようなビルドによって、誰でもcanister の作者と同じステップを踏んで、まったく同じWasmコードを作成することができます。そして、Internet Computer 上のcanister で実行されているWasmコードと比較することができます。

再現可能なビルドの話題に慣れているせっかちな読者は、そのまま[結論まで](#repro-build-summary)読み飛ばすことができます。

## Internet Computer がどのcanister コードを実行しているかを調べるだけでは、Wasm コードにアクセスすることはできません。

Internet Computer を調べても、任意の のWasmコードにアクセスすることはできません。これは、開発者がいくつかのコードを非公開にしたい場合があるため、設計上の決定です。しかし、 では、 のWasmコードのSHA-256にアクセスすることができます。canister Internet Computer canister

このハッシュを取得するには、まずInternet Computer canister のプリンシパルをメモし、そのコードをチェックする必要があります。たとえば、プリンシパルが`rdmx6-jaaaa-aaaaa-aaadq-cai` のインターネットIDcanister のコードに興味があるとします。このサービスにアクセスする最も簡単な方法は、ターミナルから[IC SDKを](https://internetcomputer.org/developers/)使用することです。ターミナルを開き、実行します：

    dfx canister --network ic info rdmx6-jaaaa-aaaaa-aaadq-cai
    Controllers: r7inp-6aaaa-aaaaa-aaabq-cai
    Module hash: 0xa9a5d0492d1455d6b6b84fdc102fb182f55975145a2f99a403eb10ea7e37aee6

古いバージョンのIC SDKを実行している場合、有効な`dfx.json` ファイルがあるディレクトリからこのコマンドを実行する必要があります。そのようなディレクトリがない場合は、`dfx new` を使って作成できます。

ここで、Internet Computer は、`rdmx6-jaaaa-aaaaa-aaadq-cai` canister (Internet Identitycanister)のWasmモジュールのハッシュが`0xa9a5d0492d1455d6b6b84fdc102fb182f55975145a2f99a403eb10ea7e37aee6` であることを示しています。

上記のチェックにより、canisterのWasmモジュールの**現在の**ハッシュが得られますが、Internet Computer canister の**コントローラは**、いつでもコードを変更できます (たとえば、canister をアップグレードする場合など)。しかし、コントローラのリストが空であるか、唯一のコントローラが[ブラックホール](https://github.com/ninegua/ic-blackhole) canister である場合、誰もコードを変更する権限を持っていないため、canister は不変であることがわかります。

このハッシュを使用して、次に、指定されたソースコードに対応しているかどうかをチェックできます。これは、そのコードのビルド・プロセスが再現可能である場合にのみ機能します。

## 再現可能なビルド

canister の作者として、あなたのビルドを再現できるようにするために、ユーザーに提供しなければならないものがいくつかあります：

- canister 用の Wasm モジュールを作成するために使用したものと同じソースコード。

- ビルド環境を再現するための手順

- ソースコードからWasmをビルドするプロセスを繰り返す方法の説明。重要なのは、プロセスが決定論的で、まったく同じWasmになることです。また、Wasmが悪意のあるビルドツールの成果物ではなく、ソースコードの忠実な翻訳であるとユーザーが確信できるような、信頼できるものでなければなりません。特に、`.dfx` 、`node_modules` 、`target` 、ビルド前のファイルを含む可能性のあるディレクトリを探してください。

次に、これらの各ポイントを詳しく見ていきます。

### ソースコードの提供

通常、`git` やその他のバージョン管理システムでコードをバージョン管理し、GitHub などの公開リポジトリでバージョン管理されたコードを利用できるようにします。この場合、Internet Computer にデプロイするコードを作成するときに使用した特定のコミットを記録し、canister を検証しようとしているユーザーに伝える必要があります。また、canister のビルドに使用したソースコードを含むパッケージ（zip ファイルや tarball など）を提供することもできます。

### ビルド環境の再現

コードをビルドする前に、使用しているビルド環境を詳細に文書化してください。特に、Internet Computer SDK がサポートする言語について：

- canister をビルドするために使用しているオペレーティング・システムとそのバージョンをメモしてください。

- IC SDK を使用している場合は、`dfx.json` で指定されている使用バージョンをメモしてください。任意のバージョンの IC SDK をインストールするには、`dfx toolchain install <version>` を使用するか、`DFX_VERSION` 環境変数を希望のバージョンに設定してインストールスクリプトを実行します。

- `dfx build` 以外の方法でMotoko コードをビルドしている場合は、使用している`moc` のバージョンに注意してください。

- Rust を構築している場合は、使用している`cargo` のバージョンに注意してください。

- フロントエンド開発に Node.js および/または`webpack` を使用している場合は、それらのバージョンをメモしてください。

- ビルドプロセスが環境変数（タイムゾーンやロケールなど）に依存している場合は、それをメモしておきます。

説明書の中で、これらすべてをユーザーに伝える必要があります。DockerやNixなどのツールを使って、ビルド環境を再現する実行可能なレシピを提供するのが理想的です。Dockerを使えば、ソフトウェアのビルドに使われたオペレーティングシステムも特定できるので、Dockerを使うことをお勧めします。

### Dockerを使ったビルド環境

[Dockerコンテナは](https://docs.docker.com/)ビルド環境を提供するための一般的なソリューションです。OS Xを使用している開発者には、[limaを](https://github.com/lima-vm/lima)使用してDockerをインストールすることをお勧めします。私たちの経験上、Docker DesktopやDocker Machineよりも安定していることが証明されているからです（特に、Apple M1マシン上のいくつかのQEMUバグを回避できます）。

Dockerをセットアップした後、`dfx` 、Node.js、Rustツールチェーンだけでなく、以下のような`Dockerfile` 、特定のバージョンのオペレーティングシステムをユーザに提供することができます。一般的にビルドはアーキテクチャ間で再現できないため、Dockerコンテナの実行には`x86_64` 。ホスト環境が`x86_64` でない場合は、クロスプラットフォームのDockerコンテナのセットアップに関する[ドキュメントを](https://github.com/lima-vm/lima/blob/master/docs/multi-arch.md)参照してください。

このDockerfileは、Dockerを使ってRustのビルド環境を規格化する方法の例です。

#### Dockerfileの例

    FROM ubuntu:22.04
    
    ENV NVM_DIR=/root/.nvm
    ENV NVM_VERSION=v0.39.1
    ENV NODE_VERSION=18.1.0
    
    ENV RUSTUP_HOME=/opt/rustup
    ENV CARGO_HOME=/opt/cargo
    ENV RUST_VERSION=1.62.0
    
    ENV DFX_VERSION=0.14.1
    
    # Install a basic environment needed for our build tools
    RUN apt -yq update && \
        apt -yqq install --no-install-recommends curl ca-certificates \
            build-essential pkg-config libssl-dev llvm-dev liblmdb-dev clang cmake rsync
    
    # Install Node.js using nvm
    ENV PATH="/root/.nvm/versions/node/v${NODE_VERSION}/bin:${PATH}"
    RUN curl --fail -sSf https://raw.githubusercontent.com/creationix/nvm/${NVM_VERSION}/install.sh | bash
    RUN . "${NVM_DIR}/nvm.sh" && nvm install ${NODE_VERSION}
    RUN . "${NVM_DIR}/nvm.sh" && nvm use v${NODE_VERSION}
    RUN . "${NVM_DIR}/nvm.sh" && nvm alias default v${NODE_VERSION}
    
    # Install Rust and Cargo
    ENV PATH=/opt/cargo/bin:${PATH}
    RUN curl --fail https://sh.rustup.rs -sSf \
            | sh -s -- -y --default-toolchain ${RUST_VERSION}-x86_64-unknown-linux-gnu --no-modify-path && \
        rustup default ${RUST_VERSION}-x86_64-unknown-linux-gnu && \
        rustup target add wasm32-unknown-unknown &&\
        cargo install ic-wasm
    
    # Install dfx
    RUN sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"
    
    COPY . /canister
    WORKDIR /canister

#### これで何ができるのか

この`Dockerfile` について、注目すべき点がいくつかあります：

- 公式のDockerイメージから起動しています。さらに、インストールされているツールは全て規格のものであり、規格のソースから来ています。これにより、ビルド環境は改ざんされておらず、Dockerを使ったビルドプロセスは信頼できるという確信をユーザーに与えます。

- 特定のバージョンのビルドツールを確実にインストールするために、`apt` （コンテナ内で動作するLinuxディストリビューションであるUbuntuのパッケージマネージャ）ではなく、直接インストールします。このようなパッケージマネージャは通常、ビルドツールを特定のバージョンに固定する方法を提供しません。

この`Dockerfile` を使用するには、Dockerを[立ち上げて実行し](https://docs.docker.com)、canister のプロジェクトディレクトリに`Dockerfile` を配置し、実行してDockerコンテナを作成します：

    docker build -t mycanister .

これにより、`mycanister` というDockerコンテナイメージが作成され、その中にNode.js、Rust、`dfx` がインストールされ、canister のソースコードが`/canister` にコピーされます（canister のプロジェクトディレクトリから`docker build` を呼び出す必要があることを思い出してください）。その後、runすることでコンテナ内の対話型シェルに入ることができます：

    docker run -it --rm mycanister

ここから、canister のビルドに必要な手順を試すことができます。手順が決定論的であると確信が持てたら、`Dockerfile` （たとえば、以下の例のようなビルドスクリプトの場合は`RUN ./build_script.sh` として、`./build_script.sh` として保存）にその手順を記述して、ユーザーがcanister のビルドを自動的に再現できるようにすることもできます。[Internet IdentityのDockerfilecanister](https://github.com/dfinity/internet-identity/blob/397d0087a29855564c47f0fd3323f60b5b67a8fa/Dockerfile) に例があります。

#### Rustプロジェクトをビルドするスクリプトの例

Rustプロジェクトをビルドするスクリプトの例は以下のようになります：

:::info
これはRustプロジェクトのビルドスクリプトの例であり、ビルドスクリプトの内容を完全に網羅しているわけではありません。また、Motoko.
::.のような他の言語のビルド手順も含まれていません：

    #!/bin/bash
    #
    # additional setup, e.g., build frontend assets:
    # ...
    # Rust build:
    export RUSTFLAGS="--remap-path-prefix $(readlink -f $(dirname ${0}))=/build --remap-path-prefix ${CARGO_HOME}=/cargo"
    cargo build --locked --target wasm32-unknown-unknown --release
    ic-wasm target/wasm32-unknown-unknown/release/example_backend.wasm -o example_backend.wasm shrink

上記のようなビルドスクリプトは、`dfx.json` のカスタムビルドスクリプトとして、次のように指定できます：

    "canisters": {
      "example_backend": {
        "candid": "src/example_backend/example_backend.did",
        "package": "example_backend",
        "type": "custom",
        "wasm": "./example_backend.wasm",
        "build": "./build_script.sh"
      }
    }

### ビルドプロセスの決定性確保

次に、ビルドを決定論的にするために必要なことを調べます。ビルドプロセスを決定論的にするには

- #### ステップ 1:canister の依存関係が常に同じ方法で解決されるようにする必要があります。現在、ほとんどのビルドツールは、依存関係を特定のバージョンに固定する方法をサポートしています。
  
  - `npm` の場合、`npm install` を実行すると、`package.json` で指定された要件を満たすプロジェクトのすべての（推移的な）依存関係の固定バージョンを含む`package-lock.json` ファイルが作成されます。しかし、`npm install` を実行するたびに、`package-lock.json` ファイルが上書きされます。したがって、canister の最終バージョンを作成する準備ができたら、`npm install` を一度だけ実行してください。その後、`package-lock.json` をバージョン管理システムにコミットしてください。最後に、ビルドの再現性をチェックするときは、`npm install` の代わりに`npm ci` を使ってください。
  
  - Rustコードの場合、Cargoは（遷移する）依存関係の固定バージョンを含む`Cargo.lock` ファイルを自動的に生成します。`package-lock.json` と同様に、canister の最終版を作成する準備ができたら、このファイルをバージョン管理システムにコミットしてください。さらに、Cargo はデフォルトで依存関係のロックされたバージョンを無視します。ロックされた依存関係が使用されるようにするには、`cargo` コマンドに`--locked` フラグを渡してください。
  
  - canisters は互いに ID で参照し合うので、canister ID をあらかじめ割り当てておく必要があります。

- #### ステップ2：独自のビルドスクリプトは、非決定性を導入してはなりません。

非決定性の明らかな原因としては、ランダム性、タイムスタンプ、同時実行性、コード難読化などがあります。あまり明らかでない原因としては、ロケール、絶対ファイルパス、ディレクトリ内のファイルの順序、内容が変更される可能性のあるリモートURLなどがあります。さらに、サードパーティのビルドプラグインに依存すると、これらによってもたらされる非決定性にさらされることになります。

- #### ステップ3.  同じ依存関係と決定論的なビルドスクリプトを考えると、ビルドツール自体も決定論的でなければなりません (Motoko 用の`moc` 、Rust 用の`cargo` 、フロントエンド開発用のデフォルトの`webpack` )。

良いニュースは、これらのツールはすべて決定論的であることを目指しているということです。しかし、これらのツールは複雑なソフトウェアであり、決定性を保証することは自明ではありません。したがって、非決定性バグは起こり得ますし、実際に起こります。Rustについては、[Rustにおける現在の潜在的な非決定性問題のリストを](https://github.com/rust-lang/rust/labels/A-reproducibility)参照してください。さらに、Linux と MacOS で Wasm にコンパイルされた Rust コードの違いが観察されたため、ビルドプラットフォームとそのバージョンを固定することを推奨します。webpack では、バージョン 5 以降、使用すべき[モジュールとチャンク ID の決定論的な命名が](https://webpack.js.org/configuration/optimization/)導入されています。Motoko コンパイラーは決定論的で再現可能であることを目指しています。再現性に問題がある場合は、[新しい課題を](https://github.com/dfinity/motoko/issues/new/choose)提出してください。

### 再現性のテスト

あなたのコードにとって再現性が重要であるならば、ビルドをテストして再現性の確信を深めるべきです。このようなテストは自明なことではありません。canister のビルドで非決定性が現れるのに1ヶ月かかったという実例を見たことがあります！幸いなことに、**Debian Reproducible Builds**プロジェクトは[reprotest](https://salsa.debian.org/reproducible-builds/reprotest) というツールを作成しました。このツールは、パスや時間、ファイルの順序などの特性が異なる 2 つの異なる環境でビルドを実行し、結果を比較することでビルドをテストします。`reprotest` を使ってビルドをチェックするには、`Dockerfile` に以下の行を追加してください：

    RUN apt -yqq install --no-install-recommends reprotest disorderfs faketime rsync sudo wabt

`dfx build --network ic` を使用する場合、フロントエンドの依存関係を事前にビルドする必要があります (たとえば、`dfx build --network ic` の前に`npm ci` を実行するか、`dfx.json` でカスタムビルドタイプを設定し、ビルドスクリプトで`npm ci` を実行します)。プロジェクトディレクトリには、Internet Computer 上のcanisters の ID を含む`canister_ids.json` ファイルがあるはずです。`canister_ids.json` ファイルの例は次のようになります：

    {
      "example_backend": {
        "ic": "rrkah-fqaaa-aaaaa-aaaaq-cai"
      },
      "example_frontend": {
        "ic": "ryjl3-tyaaa-aaaaa-aaaba-cai"
      }
    }

これで、canister プロジェクトのルートディレクトリから、次のように IC SDK ビルドの再現性をテストできます：

    docker build -t mycanister .
    docker run --rm --privileged -it mycanister
    /canister# mkdir artifacts
    /canister# reprotest -vv --store-dir=artifacts --variations '+all,-time' 'dfx build --network ic' '.dfx/ic/canisters/*/*.wasm'

#### 何をするかというと

- 最初のコマンドは、先に提供した`Dockerfile` を使用してDockerコンテナをビルドします。
- 2番目のコマンドは、コンテナ内で対話型シェルを開きます（`-it` フラグがあるのはそのためです）。`reprotest` 、ビルド環境のバリエーションによってはカーネルモジュールを使用するため、特権モードで実行します（`--privileged` フラグ）。いくつかのバリエーションを除外することで、非特権モードで実行することもできます。`--rm` フラグは、シェルを閉じた後にコンテナを破棄します。
- コンテナ内に入ったら、ビルド成果物用のディレクトリを作成し、`reprotest` を冗長モードで起動します（`-vv` フラグ）。最初の引数として、実行したいビルドコマンドを指定する必要があります。ここでは、`dfx build --network ic` とします。別のビルド・プロセスを使用している場合は調整してください。すると、2つの異なる環境でビルドが実行されます。
- 最後に、2つのビルドの最後で比較するパスを`reprotest` に指定します。ここでは、`.dfx/ic` ディレクトリにあるすべてのcanisters のWasmコードを比較します。Rust コンパイラは動的メモリ割り当てに`jemalloc` を使用しており、このライブラリは`reprotest` が時間変動を実装するために使用している`faketime` と[互換性が](https://github.com/wolfcw/libfaketime/issues/130)ないため、時間変動を省略しています。とはいえ、手動でシステム時間を変更しながら、`reprotest` によって生成された成果物を比較することをお勧めします。

比較で違いが見つからなければ、このような出力が表示されます：

    =======================
    Reproduction successful
    =======================
    No differences in ./.dfx/ic/canisters/*/*.wasm
    6b2a15a918219138836e88e9c95f9c5d2d7b6d465df83ae05d6fd2b0f14f8a97  ./.dfx/ic/canisters/example_backend/example_backend.wasm
    a047686c1d517e21d447bcd42c9394a12cdb240e06425b830c99d3a689b5ee20  ./.dfx/ic/canisters/example_frontend/assetstorage.wasm
    a047686c1d517e21d447bcd42c9394a12cdb240e06425b830c99d3a689b5ee20  ./.dfx/ic/canisters/example_frontend/example_frontend.wasm

これは、ビルドが環境の影響を受けていないことを示す良い指標です！

:::info
 `reprotest` は依存関係が正しく固定されているかどうかをチェックできないことに注意してください。さらに、コンテナ`reprotest` のビルドを複数のホスト OS で実行し、結果を比較することをお勧めします。比較の結果、2つのビルドで生成されたWasmコードに違いが見つかった場合は、diffが出力されます。この場合、`reprotest` の`--store-dir` フラグを使用して、出力と差分を分析できる場所に保存します。再現性の達成に苦労している場合は、任意のビルドを決定論的にしようとするコンテナの抽象化である[DetTraceの](https://github.com/dettrace/dettrace)使用も検討してください。
::：

ビルドの再現性を達成した後も、長期的に考慮すべきことがあります。

### 長期的な考慮事項

canister のコードが何年も残り、再現可能であり続けることを期待する場合、再現性はより厳しくなります。最大の課題は、あなたの：

- ビルドツールチェーンが将来も利用可能であること。

- 依存関係が利用可能であること。

- ツールチェーンがまだ動作し、依存関係を正しくビルドできること。

ディストリビューションやパッケージアーカイブは、あなたのツールチェインや依存関係を含む古いバージョンのパッケージを削除する可能性があります。ウェブサイトがオフラインになり、URLが機能しなくなるかもしれません。したがって、ツールチェインと依存関係をすべてバックアップしておくことが賢明です。これを大規模に行う[Software Heritageの](https://www.softwareheritage.org/)ようなプロジェクトに参加することを検討すべきです。後のある時点で、canister がまだビルドできるように、ビルドプロセスを調整しなければならないかもしれません(たとえば、URLを変更するなど)。ビルドが変更されたとしても、同じ結果が得られるのであれば、canister が正しいコードを実行していることをユーザは確信できます。あなたの依存関係がSoftware Heritageプロジェクトのような信頼できるソースから来たものであれば、信頼の議論はより簡単です。

## 結論

canister の作者への推奨事項をまとめます：

- 理想的には、あなたのコンテナコードの最終版を作成するとき、Dockerまたは同様の技術を使用して、オペレーティングシステムとビルドツールを便利にセットアップし、ユーザーのためにそれらのバージョンを修正することです。使用しているビルドツールが完全に再現可能なビルドを保証していない場合、Dockerはパスや環境変数などの違いを最小限に抑えることでも役立ちます。

- ビルドツールとベースとなるDockerイメージは、ユーザが信頼できるところから調達する必要があります。

- RustとMotoko コンパイラは決定論的であることを目指しており、再現可能なビルドをサポートします。非決定性に気づいたら、バグレポートを提出してください。

- NPM を使用する場合は、すべての依存関係のバージョンを正確に指定してください (`package_lock.json` を git リポジトリにコミットしてください\!)。`install` ではなく`ci` コマンドを使用して NPM を起動し、ビルドを再現します。同様に、Rust パッケージの場合は`Cargo.lock` をリポジトリにコミットし、パッケージのビルド時には`cargo build --locked` を使用してください。

- Webpackのビルドは決定論的であるべきですが、オブファスカレーターや同様のツールは再現性を損なう可能性があります。必ず決定論的なチャンクとモジュールIDを使用してください。

- ビルドツールは完璧ではなく、再現可能なビルドを保証できない場合があります。あなたのcanister にとって再現性が重要な場合（例えば、他のユーザーの資金を握っている場合など）、テストしてください。Reprotestはこの目的のための便利なツールです。

- 完全な監査を行うためには、ユーザはあなたの依存関係もすべて（再現可能な形で）再構築しなければならないかもしれないからです。

- 再現性を達成するのは、長い時間スケールでは難しくなります。主に、依存関係やビルドツールの信頼できるソースが利用可能であり続けることを保証する必要があるからです。

最後に、ビルドが再現可能であれば、出来上がったWasmコードのハッシュと、canister で実行されているコードのハッシュを比較することができます：

    dfx canister --network ic info <canister-id>

このハッシュは、コントローラがcanister のコードをアップグレードした場合に変更される可能性があることに注意してください。

<!---
# Creating reproducible canister builds

## Overview
Thanks to its consensus protocol, the Internet Computer always runs canister code correctly. But this doesn’t mean that it’s running the **correct** code for a canister. If you are using a canister somebody else developed, you may want to verify that the canister is indeed running the intended code before giving it control to make important decision for you, e.g., sending your ICP to another canister. Verifying this requires answering two questions:

1.  Which WebAssembly (Wasm) code is being executed for a canister?
2.  The canisters are normally written in a higher-level language, such as Motoko or Rust, and not directly in Wasm. The second question is then: is the Wasm that’s running really the result of compiling the purported source code?

The rest of the document answers these questions. For the first question, we will see how the Internet Computer provides information about canister code. To be able to answer the second question, canister authors should ensure trusted and [reproducible builds](https://reproducible-builds.org/docs/definition/) of the Wasm code from the source. Such builds allow anyone to follow the same steps as the canister authors and yield the exact same Wasm code, which can then be compared to the Wasm code executing in the canister on the Internet Computer.

The impatient reader who is familiar with the topic of reproducible builds can skip straight to the [conclusion](#repro-build-summary).

## Finding out which canister code the Internet Computer is executing

Internet Computer does not allow you to access the Wasm code of an arbitrary canister. This is a design decision, as developers might want to keep some code private. However, the Internet Computer does allow you to access the SHA-256 of the Wasm code of a canister.

To obtain this hash, you must first note the principal of the Internet Computer canister whose code you want to check. For example, assume we’re interested in the code of the Internet Identity canister, whose principal is `rdmx6-jaaaa-aaaaa-aaadq-cai`. Then, the easiest way to access this service is using the [IC SDK](https://internetcomputer.org/developers/) from the terminal. Open your terminal, and run:

```
dfx canister --network ic info rdmx6-jaaaa-aaaaa-aaadq-cai
Controllers: r7inp-6aaaa-aaaaa-aaabq-cai
Module hash: 0xa9a5d0492d1455d6b6b84fdc102fb182f55975145a2f99a403eb10ea7e37aee6
```

If you are running an older version of the IC SDK, you will need to run this command from a directory that contains a valid `dfx.json` file. If you don’t have such a directory, you can create it using `dfx new`.

Here, the Internet Computer tells us that the hash of the Wasm module of the `rdmx6-jaaaa-aaaaa-aaadq-cai` canister (which happens to be the Internet Identity canister) is `0xa9a5d0492d1455d6b6b84fdc102fb182f55975145a2f99a403eb10ea7e37aee6`.

The check above provides you the **current** hash of the canister’s Wasm module, but the **controllers** of an Internet Computer canister may change the code at any time (e.g., to upgrade a canister). However, if the list of controllers is empty or the only controller is a [black-hole](https://github.com/ninegua/ic-blackhole) canister, you know that the canister is immutable since nobody has the power to change the code.

Armed with this hash, you can next check whether it corresponds to some given source code. This only works if the build process for the code is reproducible.

## Reproducible builds

As a canister author, there are a few things you have to provide to your users to allow them to reproduce your build:

-   The same source code that you used to create the Wasm module for the canister.

-   Instructions on how to recreate your build environment.

-   Instructions on how to repeat the process of building the Wasm from the source code. Crucially, the process must be deterministic, to ensure that it results in the exact same Wasm. It also has to be trusted, such that the user can be convinced that the Wasm is a faithful translation of the source code, and not an artifact of a malicious build tool. In particular, look for `.dfx`, `node_modules`, and `target` directories that could contain pre-built files.

We next look at each of these points in more detail.

### Providing source code

Typically, you will version your code in `git` or some other version control system, and your versioned code may be available in a public repository, e.g., GitHub. In this case, you should note the particular commit that you used when producing the code to be deployed to the Internet Computer, and communicate it to the users who are trying to verify your canister. Alternatively, you can also provide a package (e.g., a zip file or a tarball) containing the source code you used to build your canister.

### Reproducing build environments

Before building your code, you should document the build environment you are using in detail. In particular, for the languages supported by the Internet Computer SDK:

-   Note down the operating system and its version you are using to build your canister.

-   If you are using the IC SDK, note the version used, as specified in `dfx.json`. You can install arbitrary versions of the IC SDK using `dfx toolchain install <version>`, or by running the installation script with the `DFX_VERSION` environment variable set to the desired version.

-   If you are building Motoko code in a way other than `dfx build`, note the version of `moc` you are using.

-   If you are building Rust, note the version of `cargo` you are using.

-   If you are using Node.js and/or `webpack` for frontend development, note their versions.

-   If your build process depends on any environment variables (such as the time zone or locale), note them down.

You should communicate all of these to your user in the instructions. Ideally, do so by providing an executable recipe to recreate the build environment, using tools such as Docker or Nix. We recommend using Docker, as this allows you to also pinpoint the operating system used for building the software.

### Build environments using Docker

[Docker containers](https://docs.docker.com/) are a popular solution for providing build environments. For developers using OS X, we recommend installing Docker using [lima](https://github.com/lima-vm/lima), as it proved more stable than Docker Desktop or Docker Machine in our experience (in particular, it avoids some QEMU bugs on Apple M1 machines). 

After setting Docker up, you can use a `Dockerfile` such as the following to provide the user with a particular version of the operating system, as well as `dfx`, Node.js and the Rust toolchain. Make sure to stick with `x86_64` for running the Docker container, as builds are generally not reproducible across architectures. See [docs](https://github.com/lima-vm/lima/blob/master/docs/multi-arch.md) on setting up cross-platform Docker containers in case your host environment is not `x86_64`.

This Dockerfile is an example of how a Rust build environment can be standardized using Docker. 

#### Example Dockerfile

    FROM ubuntu:22.04

    ENV NVM_DIR=/root/.nvm
    ENV NVM_VERSION=v0.39.1
    ENV NODE_VERSION=18.1.0

    ENV RUSTUP_HOME=/opt/rustup
    ENV CARGO_HOME=/opt/cargo
    ENV RUST_VERSION=1.62.0

    ENV DFX_VERSION=0.14.1

    # Install a basic environment needed for our build tools
    RUN apt -yq update && \
        apt -yqq install --no-install-recommends curl ca-certificates \
            build-essential pkg-config libssl-dev llvm-dev liblmdb-dev clang cmake rsync

    # Install Node.js using nvm
    ENV PATH="/root/.nvm/versions/node/v${NODE_VERSION}/bin:${PATH}"
    RUN curl --fail -sSf https://raw.githubusercontent.com/creationix/nvm/${NVM_VERSION}/install.sh | bash
    RUN . "${NVM_DIR}/nvm.sh" && nvm install ${NODE_VERSION}
    RUN . "${NVM_DIR}/nvm.sh" && nvm use v${NODE_VERSION}
    RUN . "${NVM_DIR}/nvm.sh" && nvm alias default v${NODE_VERSION}

    # Install Rust and Cargo
    ENV PATH=/opt/cargo/bin:${PATH}
    RUN curl --fail https://sh.rustup.rs -sSf \
            | sh -s -- -y --default-toolchain ${RUST_VERSION}-x86_64-unknown-linux-gnu --no-modify-path && \
        rustup default ${RUST_VERSION}-x86_64-unknown-linux-gnu && \
        rustup target add wasm32-unknown-unknown &&\
        cargo install ic-wasm

    # Install dfx
    RUN sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"

    COPY . /canister
    WORKDIR /canister

#### What this does
There are a couple of things worth noting about this `Dockerfile`:

-   It starts from an official Docker image. Furthermore, all the installed tools are standard, and come from standard sources. This provides the user with confidence that the build environment hasn’t been tampered with, and thus that the build process using Docker can be trusted.

-   To ensure that specific versions of the build tools are installed, it installs them directly, rather than through `apt` (the package manager of Ubuntu, the Linux distribution running inside of the container). Such package managers usually don’t provide a way of pinning the build tools to specific versions.

To use this `Dockerfile`, get Docker [up and running](https://docs.docker.com), place the `Dockerfile` in the project directory of your canister, and create the Docker container by running:

    docker build -t mycanister .

This creates a Docker container image called `mycanister`, with Node.js, Rust and `dfx` installed in it, and your canister source code copied to `/canister` (recall that you should invoke `docker build` from the canister project directory). You can then enter an interactive shell inside of your container by running:

    docker run -it --rm mycanister

From here, you can experiment with the steps needed to build your canister. Once you are confident that the steps are deterministic, you can also put them in the `Dockerfile` (e.g., as `RUN ./build_script.sh` for a build script such as the example below, saved as `./build_script.sh`), to allow the user to automatically reproduce the build of your canister. You can see an example in the [Dockerfile of the Internet Identity canister](https://github.com/dfinity/internet-identity/blob/397d0087a29855564c47f0fd3323f60b5b67a8fa/Dockerfile). 

#### Example script for building a Rust project
An example script to build a Rust project looks as follows:

:::info
This is an example build script for a Rust project, and does not include a fully comprehensive example of what a build script may contain, nor does it include build instructions for other languages such as Motoko. 
:::

    #!/bin/bash
    #
    # additional setup, e.g., build frontend assets:
    # ...
    # Rust build:
    export RUSTFLAGS="--remap-path-prefix $(readlink -f $(dirname ${0}))=/build --remap-path-prefix ${CARGO_HOME}=/cargo"
    cargo build --locked --target wasm32-unknown-unknown --release
    ic-wasm target/wasm32-unknown-unknown/release/example_backend.wasm -o example_backend.wasm shrink

A build script such as the one above can be specified as a custom build script in `dfx.json` as follows:

    "canisters": {
      "example_backend": {
        "candid": "src/example_backend/example_backend.did",
        "package": "example_backend",
        "type": "custom",
        "wasm": "./example_backend.wasm",
        "build": "./build_script.sh"
      }
    }



### Ensuring the determinism of the build process
Next, we will investigate what is necessary to make the build deterministic. For the build process to be deterministic:

- #### Step 1:  You will need to ensure that any dependencies of your canister are always resolved in the same way. Most build tools now support a way of pinning dependencies to a particular version.

    -   For `npm`, running `npm install` will create a `package-lock.json` file with some fixed versions of all (transitive) dependencies of your project that satisfy the requirements specified in your `package.json`. However, `npm install` will overwrite the `package-lock.json` file every time it is invoked. Thus, once you are ready to create the final version of your canister, run `npm install` only once. After that, commit `package-lock.json` to your version control system. Finally, when checking the build for reproducibility, use `npm ci` instead of `npm install`.

    -   For Rust code, Cargo will automatically generate a `Cargo.lock` file with the fixed versions of your (transitive) dependencies. Like with `package-lock.json`, you should commit this file to your version control system once you are ready to produce the final version of your canister. Furthermore, Cargo by default ignores the locked versions of dependencies. Pass the `--locked` flag to the `cargo` command to ensure that the locked dependencies are used.

    -   You have to allocate canister IDs in advance, as canisters refer to each other by their IDs.

- #### Step 2:  Your own build scripts must not introduce non-determinism. 
Obvious sources of non-determinism include randomness, timestamps, concurrency, or code obfuscators. Less obvious sources include locales, absolute file paths, order of files in a directory, and remote URLs whose content can change. Furthermore, relying on third-party build plug-ins exposes you to any non-determinism introduced by these.

- #### Step 3.  Given the same dependencies and deterministic build scripts, the build tools themselves (`moc` for Motoko, `cargo` for Rust, `webpack` by default for frontend development) must also be deterministic. 
The good news is that all of these tools aim to be deterministic. However, they are complicated pieces of software, and ensuring determinism is non-trivial. Thus, non-determinism bugs can and do occur. For Rust, see the [list of current potential non-determinism issues in Rust](https://github.com/rust-lang/rust/labels/A-reproducibility). Furthermore, we have observed differences between Rust code compiled to Wasm under Linux and MacOS, and thus recommend pinning the build platform and its version. For webpack, [deterministic naming of module and chunk IDs](https://webpack.js.org/configuration/optimization/) that you should use have been introduced since version 5. The Motoko compiler aims to be deterministic and reproducible; if you find reproducibility issues, please submit a [new issue](https://github.com/dfinity/motoko/issues/new/choose), and we will try to address them to the extent possible.

### Testing reproducibility

If reproducibility is vital for your code, you should test your builds to increase your confidence in their reproducibility. Such testing is non-trivial: we have seen real-world examples where non-determinism in a canister build took a month to show up! Fortunately, the **Debian Reproducible Builds** project created a tool called [reprotest](https://salsa.debian.org/reproducible-builds/reprotest), which can help you automate reproducibility tests. It tests your build by running it in two different environments that differ in characteristics such as paths, time, file order, and others, and comparing the results. To check your build with `reprotest`, add the following line to your `Dockerfile`:

    RUN apt -yqq install --no-install-recommends reprotest disorderfs faketime rsync sudo wabt

When using `dfx build --network ic`, you need to prebuild your frontend dependencies (e.g., by running `npm ci` before `dfx build --network ic` or by setting the custom build type in `dfx.json` and running `npm ci` in your build script) and your project directory should contain a `canister_ids.json` file containing the IDs of your canisters on the Internet Computer. An example `canister_ids.json` file looks as follows:

    {
      "example_backend": {
        "ic": "rrkah-fqaaa-aaaaa-aaaaq-cai"
      },
      "example_frontend": {
        "ic": "ryjl3-tyaaa-aaaaa-aaaba-cai"
      }
    }

Now, from the root directory of your canister project, you can test the reproducibility of your IC SDK builds as follows:

    docker build -t mycanister .
    docker run --rm --privileged -it mycanister
    /canister# mkdir artifacts
    /canister# reprotest -vv --store-dir=artifacts --variations '+all,-time' 'dfx build --network ic' '.dfx/ic/canisters/*/*.wasm'

#### What this does
- The first command builds the Docker container using the `Dockerfile` provided earlier. 
- The second one opens an interactive shell (hence the `-it` flags) in the container. We run this in privileged mode (the `--privileged` flag), as `reprotest` uses kernel modules for some build environment variations. You can also run it in non-privileged mode by excluding some of the variations; see the [reprotest manual](https://manpages.debian.org/stretch/reprotest/reprotest.1.en.html). The `--rm` flag will destroy the container after you close its shell. 
- Once inside of the container, we create a directory for the build artifacts and launch `reprotest` in verbose mode (the `-vv` flags). You need to give it the build command you want to run as the first argument. Here, we assume that it’s `dfx build --network ic` - adjust it if you’re using a different build process. It will then run the build in two different environments. 
- Finally, you need to tell `reprotest` which paths to compare at the end of the two builds. Here, we compare the Wasm code for all canisters, which is found in the `.dfx/ic` directory. We omit the time variation because the Rust compiler uses `jemalloc` for dynamic memory allocation and this library is not [compatible](https://github.com/wolfcw/libfaketime/issues/130) with `faketime` used by `reprotest` to implement the time variation. Nevertheless, we encourage you to compare the artifacts produced by `reprotest` while manually changing your system time.

If the comparison doesn’t find any differences, you will see an output similar to this one:

    =======================
    Reproduction successful
    =======================
    No differences in ./.dfx/ic/canisters/*/*.wasm
    6b2a15a918219138836e88e9c95f9c5d2d7b6d465df83ae05d6fd2b0f14f8a97  ./.dfx/ic/canisters/example_backend/example_backend.wasm
    a047686c1d517e21d447bcd42c9394a12cdb240e06425b830c99d3a689b5ee20  ./.dfx/ic/canisters/example_frontend/assetstorage.wasm
    a047686c1d517e21d447bcd42c9394a12cdb240e06425b830c99d3a689b5ee20  ./.dfx/ic/canisters/example_frontend/example_frontend.wasm

Congratulations - this is a good indicator that your build is not affected by your environment! 

:::info
Note that `reprotest` can’t check that your dependencies are pinned properly; use guidelines from the previous section for that. Moreover, we recommend you to run the container `reprotest` builds under several host operating systems and compare the results. If the comparison does find differences between the Wasm code produced in two builds, it will output a diff. You will then likely want to use the `--store-dir` flag of `reprotest` to store the outputs and the diff somewhere where you can analyze them. If you are struggling to achieve reproducibility, consider also using [DetTrace](https://github.com/dettrace/dettrace), which is a container abstraction that tries to make arbitrary builds deterministic.
:::

Even after you achieve reproducibility for your builds, there are still other things to consider for the long term.

### Long-term considerations

Reproducibility can be more demanding if you expect your canister code to stay around for years, and stay reproducible. The biggest challenges are to ensure that your:

-  Build toolchain is still available in the future.

-  Dependencies are available.

-  Toolchain still runs and still correctly builds your dependencies.

Distributions and package archives may drop old versions of packages, including both your toolchain and their dependencies. Web sites may go offline and URLs might stop working. Thus, it’s prudent to back up all of your toolchain and dependencies. You should consider getting involved in projects such as [Software Heritage](https://www.softwareheritage.org/), which do this on a large scale. At some later point in time, you might have to adjust your build process (e.g., by changing URLs) to ensure that your canister still builds. Even if the build changes, if it still yields the same result, your users can be confident that your canister is running the correct code. The trust argument is easier if your dependencies come from a trustworthy source, such as the Software Heritage project.

## Conclusion

Summarizing our recommendations for canister authors:

-   Ideally, when producing the final version of your container code, use Docker or a similar technology to conveniently set up the operating system and the build tools, and fix their versions for the user. If the build tools you are using don’t guarantee fully reproducible builds, Docker can also help by minimizing the differences in paths, environment variables etc.

-   The build tools and the base Docker image should be sourced from somewhere that the user can trust.

-   Rust and Motoko compilers aim to be deterministic, and thus to support reproducible builds. If you notice non-determinism, file bug reports.

-   When using NPM, ensure that you specify the exact versions of all your dependencies (commit `package_lock.json` to your git repo!). Invoke NPM using the `ci` command rather than `install` to reproduce the build. Similarly, for Rust packages commit `Cargo.lock` to your repository, and then use `cargo build --locked` when building the package.

-   Webpack builds should be deterministic, but obfuscators and similar tools may compromise reproducibility. Make sure you use deterministic chunk and module IDs.

-   Build tools aren’t perfect, and may fail to ensure reproducible builds. If reproducibility is critical for your canister (e.g., it holds other users' funds), test it. Reprotest is a useful tool for this purpose.

-   Ideally, you want to minimize the number of dependencies, as, in order to do a full audit, the user may have to (reproducibly) rebuild all of your dependencies too.

-   Achieving reproducibility is harder over longer time scales, primarily as you need to ensure that a trustworthy source of your dependencies and build tools stays available.

Finally, if your build is reproducible, you can compare the hash of the resulting Wasm code to the hash of the code that is running in a canister, which you retrieve as follows:

    dfx canister --network ic info <canister-id>

Beware that this hash might change if the controllers upgrade the canister code.

-->
