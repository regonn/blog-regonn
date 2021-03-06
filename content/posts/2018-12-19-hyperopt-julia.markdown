---
layout: post
title: "Hyperopt.jl でハイパーパラメータの最適な値を探す(Julia 1.0)"
date: 2018-12-19 00:00:00 +0900
categories: "data-science"
desc: "Optuna がリリースされたので、Juliaのハイパーパラメータを探す Hyperopt.jl を使ってみました。"
keywords: "Julia DecisionTree Hyperopt MNIST"
gist: true
slug: 'data-science'
tags:
  - 'julia'
  - 'hyperopt'
aliases:
  - '/julia/2018/12/19/hyperopt.html'
gist: true
---

[機械学習 Advent Calendar 2018 \- Adventar](https://adventar.org/calendars/3119) の19日目の記事です。

## Optuna

[ハイパーパラメータ自動最適化ツール「Optuna」公開 \| Preferred Research](https://research.preferred.jp/2018/12/optuna-release/)

Optunaが公開されて、Julia言語でもハイパーパラメータ自動最適化ツールを探してたら、[baggepinnen/Hyperopt\.jl](https://github.com/baggepinnen/Hyperopt.jl) が一番求めているものに近かったので触ってみました。

## MNIST 問題
皆大好きMNIST。ちなみに、Juliaだと以前は [MNIST\.jl](https://github.com/johnmyleswhite/MNIST.jl) からデータが取得できたけどメンテされてなく、Julia 1.0 だと動かないので、[MLDatasets\.jl](https://github.com/JuliaML/MLDatasets.jl) を利用する。

## 試してみたコード
[JuliaのランダムフォレストライブラリDecisionTree\.jlでMNIST](https://masatoi.github.io/2017/07/10/julia-random-forest-mnist) を参考に Julia 1.0 でも動くようにしていく。

Hyperopt.jl は対応ライブラリとかなく、ランダムで複数のハイパーパラメータの選択した範囲を試してくれて、一番良い結果を返してくれるだけなので、色々使える気もするけど、結局ランダムなので、ある程度値の範囲は絞ってあげる必要がありそう。

<amp-gist
  data-gistid="4e4fa62af6471bae0a3b04482b54ea32"
  layout="fixed-height"
  height="225">
</amp-gist>

無事に正答率は上がったけど、過学習の可能性もあるので、そこら辺は工夫が必要そう。
とりあえず、雰囲気と使い方がわかったので良しとする。

それよりも、もっと Optuna で遊んでみたい欲がある。
こんな感じでハイパーパラメータも簡単に設定できていけるといいですね。
