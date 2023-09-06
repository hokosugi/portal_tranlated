# セキュリティのベストプラクティス

## 概要

この文書では、canisters と、Internet Computer 上のcanisters によって提供されるウェブアプリを開発するためのセキュリティのベストプラクティスを提供します。これらのベストプラクティスは、主にセキュリティレビューで発見された問題から着想を得ています。

私たちは、これらのベストプラクティスを開発者に公表することで、潜在的な問題に、新しいdapps の開発中 の早い段階で対処できるようにしたいと思います。理想的には、これによって、安全なdapps の開発がより効率的になります。

ここにリンクされているいくつかの優れたベストプラクティス（canister ）は、[効果的な「さび」](https://mmapped.blog/posts/01-effective-rust-canisters.html)（[ canisters ）と「さび」の](https://mmapped.blog/posts/01-effective-rust-canisters.html) [監査方法（Internet Computer canister ）です。それぞれのベストプラクティスに、関連する部分がリンクされています。](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)

## ベストプラクティスの概要

この後のページで、以下のセキュリティのベストプラクティスを提供します：

- [一般的な](./general-security-best-practices.md)セキュリティのベストプラクティス。

- 一般的なセキュリティの[ベストプラクティス](./web-app-development-security-best-practices.md)。

- [Canister 開発セキュリティの](./rust-canister-development-security-best-practices.md)ベストプラクティス。

## 対象読者

この文書は、当初はDFINITY の内部利用を目的としていました。しかし、すべての開発者が恩恵を受けられるように、現在ではコミュ ニティに公開しています。対象読者は、canisters またはInternet Computer 用のウェブアプリに取り組んでいるすべての開発者です。また、このようなコードのレビューを行っているすべての人にとっても興味深いものです。

## 免責と制限

私たちはベストプラクティスのコレクションを提供します。私たちは、これがInternet Computer 上のdapps のセキュリティを向上させるために有用であると考えていますが、このようなリストが完全であることはなく、潜在的なセキュリティ上の懸念をすべてカバーすることはないことを指摘しておきたいと思います。例えば、一般的なベストプラクティスではカバーできない、dapps のユースケースに非常に特化した攻撃ベクトルが常に存在します。したがって、ベストプラクティスに従うことは、セキュリティレビューを補完することはできても、置き換えることはできません。特に、セキュリティ上重要なdapps 場合には、セキュリティレビュー／監査を実施することを推奨します。さらに、ベストプラクティスは、現在のところ、リスクや優先順位に従って順序付けされていないことに注意してください。

<!---
# Security best practices

## Overview

This document provides security best practices for developing canisters and web apps served by canisters on the Internet Computer. These best practices are mostly inspired by issues found in security reviews.

We would like to advertise these best practices to developers so that potential issues can be addressed early during the development of new dapps, and not only in the end when (if at all) a security review is done. Ideally, this will make the development of secure dapps more efficient.

Some excellent canister best practices linked here are from [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html) and [how to audit an Internet Computer canister](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister). The relevant sections are linked in the individual best practices.

## Best Practices Overview

We provide the following security best practices in the subsequent pages:

-   [General security best practices](./general-security-best-practices.md).

-   [Web app specific security best practices](./web-app-development-security-best-practices.md).

-   [Canister development security best practices](./rust-canister-development-security-best-practices.md).

## Target Audience

This document was initially intended for internal use at DFINITY. However, we now publish this to the community so every developer can benefit. The target audience are any developers working on canisters or web apps for the Internet Computer. This is also of interest to anyone doing reviews of such code.

## Disclaimers and Limitations

We provide a collection of best practices that may grow over time. While we think this is useful to improve security of dapps on the Internet Computer, we’d like to point out that such a list will never be complete and will never cover all potential security concerns. For example, there will always be attack vectors very specific to a dapps use cases that cannot be covered by general best practices. Thus, following the best practices can complement, but not replace security reviews. Especially for security critical dapps we recommend performing security reviews/audits. Furthermore, please not that the best practices are currently not ordered according to risk or priority.

-->
