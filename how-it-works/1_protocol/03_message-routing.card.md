---

title: Message routing
---
![](/img/how-it-works/messaging-routing.webp)

# メッセージ・ルーティング

各 IC ラウンドで、メッセージ・ルーティング・コンポーネントはコンセンサスから処理されるメッセー ジのブロック（サブネットの各ノードで同じブロック）を受け取り、そのメッセージをターゲットcanisters の入力キューに入れます。そして、実行されたcanisters の出力キューに新しいcanister メッセージが入る可能性のある実行ラウンドをトリガーします。実行が完了すると、出力キュー内のメッセージは、メッセージルーティング コンポーネントによって受信者にルーティングされます。

受信者は、異なるサブネット上に存在するcanisters を含んでもよい。メッセージ・ルーティング・レイヤは、サブネット間のcanister メッセージのルーティングを実装し、それらのメッセージがブロックに含まれ、受信者のサブネットに誘導されるようにします。これはクロスサブネットメッセージング、または単にXNetメッセージングと呼ばれます。*セキュアなXNet*メッセージングは、疎結合サブネットのアーキテクチャにとって重要な要素であり、ICのスケーラビリティの前提条件です。

メッセージ・ルーティング・レイヤによって実装されるもう1つの重要な機能は、*ステート認証*です。つまり、サブネットはラウンドごとに、複製されたサブネットのステートの一部を分散型で認証します。特にこの認証は、他のサブネットがサブネット間ストリームの真正性を検証したり、ユーザーが以前に送信したメッセージの処理状況を認証的に読み取ったりするために使用されます。ステート認証とセキュアなXNetメッセージングにより、特に、複数のシャードを持つブロックチェーンが直面する課題である、サブネットの境界を越えたcanisters のセキュアで透過的な通信が可能になります。

[より深く](/how-it-works/message-routing/)

<!---


![](/img/how-it-works/messaging-routing.webp)

# Message routing

In every IC round, the message routing component receives a block of messages to be processed from consensus – the same block on each node of the subnet – and places the messages into the input queues of their target canisters, a process called *induction*. Then, it triggers the execution round which will potentially lead to new canister messages in the executed canisters' output queues. Once execution is done, the messages in the output queues are routed by the message routing component to the recipients. 

The recipients may include canisters residing on a different subnet. The message routing layer implements the routing of inter-canister messages between subnets, such that those messages can be included in blocks and be inducted on the recipient's subnet. This is referred to as cross-subnet messaging or simply XNet messaging. *Secure XNet messaging* is a key ingredient for the architecture of loosely-coupled subnets and thus a prerequisite for the scalability of the IC.

Another crucial functionality implemented by the message routing layer is *state certification*, that is, the subnet certifying parts of the replicated subnet state in every round in a decentralized manner. Among others, this certification is used by other subnets to verify the authenticity of the subnet-to-subnet streams or to allow users to authentically read the processing status of messages previously submitted by them. State certification and secure XNet messaging enable, among others, the secure and transparent communication of canisters across subnet boundaries, a challenge that any blockchain that has multiple shards struggles with. 

[Go deeper](/how-it-works/message-routing/)

-->
