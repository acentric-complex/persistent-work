# Persistent Work

[English](README.md) | **日本語**

**Persistent Work** は、1回の実行、1つのセッション、特定のモデルやexecutorをまたいでも、AIの仕事を継続可能にするための最小・ベンダー中立Protocolです。

> **Work outlives run, session, model, and executor.**  
> Workは、run・session・model・executorより長く生きる。

この日本語READMEは、人間が概念と使い方を素早く理解するための入口です。  
**AI executor向けの規範的Protocolは [AI_WORK.md](AI_WORK.md) を正本として参照してください。**

## なぜ必要か

AIは長い多段階作業を実行できるようになっていますが、実行はさまざまな理由で止まります。

- sessionの終了
- model / executorの交代
- quotaや外部service待ち
- Human Gate
- 中断や失敗

Persistent Workでは、仕事の状態をprovider sessionの中ではなく、repository上の小さなDurable Work Objectに残します。Git historyをcheckpoint historyとして使うことで、別run・別session・別executorから再開できます。

## 最小モデル

実行中のWork Objectは `WORK.yaml` です。

状態は4つだけです。

```text
ACTIVE      次のactionをAIが所有する
WAITING     人間以外の外部条件を待っている
HUMAN_GATE  人間の判断・権限付与だけが解除できる
DONE        完了条件を満たした
```

`ACTIVE` で実行可能な次actionがある限り、人間が「続けて」と言う必要はありません。

## まず見る場所

- [AI_WORK.md](AI_WORK.md) — machine-firstの規範的Protocol
- [examples/ownership-canary/WORK.yaml](examples/ownership-canary/WORK.yaml) — 完了済みWork Objectの最小例
- [examples/ownership-canary/result.txt](examples/ownership-canary/result.txt) — その成果物

freshなAI executorがこのrepositoryへ来たとき、READMEから `AI_WORK.md` を見つけ、`WORK.yaml` を初期化または再開し、実行・検証・checkpointを行い、`WAITING` / `HUMAN_GATE` / `DONE` のいずれかまで自律的に進めることを狙っています。

## 設計原則

- **session stateよりdurable state**
- **ACTIVE中はAIがexecutionを所有する**
- **Human GateはWork所有権の移管ではなく、判断権限の一時移管**
- **外部待ちはHuman Gateと分ける**
- **DONEの前にEvidenceを残す**
- **Git historyを既定のcheckpoint historyにする**
- **実際の失敗Evidenceが出るまで、余計な基盤を足さない**

## 何を作らないか

Persistent Workは、最初から以下を作る構想ではありません。

- SaaS platform
- daemon
- queue / orchestrator
- proprietary database
- provider固有のsession layer
- subagent framework
- plugin system
- 大規模SDK

provider固有のharnessはexecutorとして利用できますが、**Work stateの正本にはしません。**

## Acentric Complex

Persistent Workは **Acentric Complex** から公開しています。

**Local autonomy. Shared boundaries. Emergent intelligence.**

局所の自律性を保ち、共有境界で接続し、全体として知性が立ち上がる。  
Persistent Workは、その思想を「仕事の状態と所有権」に適用した最小Protocolです。

## License

MIT Licenseで公開しています。商用利用・改変・再配布を含め、広く利用できます。利用条件や免責事項の詳細は [LICENSE](LICENSE) を参照してください。
