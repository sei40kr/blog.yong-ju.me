---
title: "ワークスアプリケーションズで何があったのか"
---

自分は新卒でワークスアプリケーションズ(以下WAP)に入社しソフトウェアエンジニアとして勤務していた。

自分が退職したのはWAP経営の視界不良が囁かれる1年ほど前である。しかし、新プロダクトの開発に携わる部署にいたので開発状況についてはある程度知っており、何よりネット上で既に囁かれている情報が 憶測だらけ or 会社あるいは元代表への憂さ晴らし or 会社あるいは元代表に気兼ねするあまり中身がない記事が散見されたので既に公知となった情報に自分の憶測を交えて書くことにした。

# 社運をかけた新プロダクト

WAPは既存のプロダクト COMPANY に代わる新プロダクトを発表した。

> まるで有能な秘書

一見するとAIブームに乗っかっただけの一発プロダクトに思えるが、画像認識による入力の自動化やあいまい検索、そして 100ms という超高速な応答速度など、ユーザー体験を高めるための具体的な機能を伴ったプロダクト企画だった。

ちなみに Google 検索のレスポンスタイムが 400ms 程度である。これの 1/4 をターンアラウンドで実現するとなると開発の困難さがお分かり頂けるだろう。

業務システムというのは意外にもパフォーマンス悪化の要素を多く抱えている。まずレコードは基本的に論理削除なのでレコード数は増えていく一方だ。またデータの一貫性やトランザクションの独立性, ネットワーク分断耐性を犠牲にすることができないため分散システムと相性が悪い。実は殆どの業務はリアルタイム整合性を必要としていないのだが、ユーザー側からすると「結果整合性」という言葉の響きから認められない上、 業務をよく熟知して設計し、一時的な不整合が業務に悪影響を与えないことを確認する必要がある。

これらの制約の元、業務システムをWebアプリケーションとして開発し、ターンアラウンドで 100ms を目指すのであれば、ビューのレンダリングすらネックになるのである。実際に独自のビューエンジンも開発していた。

# 再上場の目論見

自分が新卒入社した年の新卒入社はグローバルで1000人近くいた。
非常に単純な計算だが、年俸500万(インターン入社は600) で1000人採用となるとそれだけで50億かかる。社会保障やオフィス賃料, 1000人の研修にかかるトータルコストを考えると、売上500億の会社にとっても非常に巨額の投資である。

恐らくWAPはその資金を再上場によって調達しようとしていたようだが、赤字経営となった上に「開発未完の巨大プロジェクト」というリスクを抱えた会社を証券取引所は上場承認しない。

そこで「なるべく早く新プロダクトの売上を計上しよう」となると「新プロダクトを早く納品しよう」という結論にたどり着く。しかし実稼働にあたって機能が不足しているシステムを受け入れて稼働させるというのは顧客にとってメリットが全くない。そこで旧プロダクトのクローンプロダクト (以下 Conv) をつくり、新プロダクトで不足している機能を Conv で補い、新プロダクトを早いタイミングで稼働可能にする方針に転換した。

# エンジニアリングへの影響

PoC から短期間でデリバリーに飛ぶようなスケジュール変更がもたらす弊害は大きい。そして2つのシステムを同期しながらデュアルで運用することも大きな困難を伴う。2つのシステムが RDB only と NoSQL といった異なるの永続化手段をもっていれば尚更である。

まず新製品と Conv のデータ同期の不具合が多発した。データ変換ロジックの誤りもあれば、新システムのバッチのスループットに同期が追いつかずキュー詰まりなど。

加えて当時のシステム基盤は、新プロダクトが謳っている要件を本当に達成できるかを実証するための PoC 実装が多かった。当初の実装はマルチテナント非対応だったり、モノリシックサービス構成だったりと、インフラコストやCASEの観点からもすぐに実運用に耐えるものではなかった。高度な技術基盤を担当している海外エンジニアの離職も多く、コンタクトを取ろうとすると既に不在という事態もしばしば発生した。

更にプロダクト開発のデスマーチ化が進んでいった。一般的にデリバリーが始まったプロダクトの遅延に対して長時間稼働で遅れを取り戻すというのはほぼ不可能なように思う。それはエンジニアに「不具合をマージしてでも終業し帰宅する」というインセンティブを与え、 **不具合と共にデリバリーされた要件は CS やコンサルを巻き添えにし、対応工数を増大させて戻ってくる。**

この木なんの木。