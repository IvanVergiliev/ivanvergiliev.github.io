---
layout: post
title: "Историята на едно изследване"
modified:
categories: 
excerpt: "Или как, и защо точно така, преброихме уникалните думи на Вазов"
tags: []
image:
  feature:
date: 2015-02-03T02:38:28+02:00
---

<script type="text/javascript" src="/assets/js/vendor/Chart.min.js"></script>
<div
  class="fb-like"
  data-share="true"
  data-width="450"
  data-show-faces="true">
</div>

{% include vocab_unique.json %}

<canvas id="lemmasIgnoreMissing" width="550" height="400"></canvas>
<script type="text/javascript">
  var lemmasIgnoreMissingCtx = document.getElementById('lemmasIgnoreMissing').getContext('2d');
  var options = {
    scaleLabel: " <%= value %>",
    scaleShowVerticalLines: false,
    barValueSpacing: 10,
    scaleFontStyle: 'bold',
    scaleFontSize: 14,
    scaleFontColor: '#444',
    responsive: true,
    scaleStartValue: 0
  };

  var authorNames = ['Иван Вазов', 'Вера Мутафчиева', 'Блага Димитрова', 'Емилиян Станев', 'Димитър Димов', 'Елин Пелин', 'Йордан Радичков', 'Йордан Йовков', 'Димитър Талев'];
  authorNames.reverse();
  var lemmasIgnoreMissing = [12858, 13071, 13240, 11956, 11811, 11093, 10737, 9916, 9282];
  lemmasIgnoreMissing.reverse();

  var totalUniqueChart = new Chart(lemmasIgnoreMissingCtx).Bar({
    labels: authorNames,
    datasets: [{
      fillColor: "rgba(151,187,205,0.8)",
      data: lemmasIgnoreMissing
    }]
  }, options);
</script>

<canvas id="lemmasAddMissing" width="550" height="400"></canvas>
<script type="text/javascript">
  var lemmasAddMissingCtx = document.getElementById('lemmasAddMissing').getContext('2d');
  var options = {
    scaleLabel: " <%= value %>",
    scaleShowVerticalLines: false,
    barValueSpacing: 10,
    scaleFontStyle: 'bold',
    scaleFontSize: 14,
    scaleFontColor: '#444',
    responsive: true,
    scaleStartValue: 0
  };

  var authorNames = ['Иван Вазов', 'Вера Мутафчиева', 'Блага Димитрова', 'Емилиян Станев', 'Димитър Димов', 'Елин Пелин', 'Йордан Радичков', 'Йордан Йовков', 'Димитър Талев'];
  authorNames.reverse();
  var lemmasAddMissing = [17856, 15295, 14756, 14022, 13083, 13516, 12886, 12117, 11321];
  lemmasAddMissing.reverse();

  var totalUniqueChart = new Chart(lemmasAddMissingCtx).Bar({
    labels: authorNames,
    datasets: [{
      fillColor: "rgba(151,187,205,0.8)",
      data: lemmasAddMissing
    }]
  }, options);
</script>

<canvas id="uniqueCoef" width="550" height="600"></canvas>
<script type="text/javascript">
  var uniqueCoefCtx = document.getElementById('uniqueCoef').getContext('2d');
  var options = {
    scaleLabel: " <%= ~~(value * 100) %> %",
    scaleShowVerticalLines: false,
    barValueSpacing: 10,
    scaleFontStyle: 'bold',
    scaleFontSize: 14,
    scaleFontColor: '#444',
    responsive: true,
    scaleOverride: true,
    scaleStartValue: 0,
    scaleStepWidth: 0.02,
    scaleSteps: 11,
    datasetFill: false,
    multiTooltipTemplate: "<%= datasetLabel %> - <%= ~~(value * 100) %> %"
  };

  var authorNames = ['Иван Вазов', 'Вера Мутафчиева', 'Блага Димитрова', 'Емилиян Станев', 'Димитър Димов', 'Елин Пелин', 'Йордан Радичков', 'Йордан Йовков', 'Димитър Талев'];
  authorNames.reverse();

  var colors = ['rgba(151,187,205,1)', 'rgba(121,167,245,1)', 'rgba(181,137,205,1)', 'rgba(251,187,205,1)', 'rgba(151,237,205,1)', 'rgba(121,207,205,1)', 'rgba(51,187,235,1)', 'rgba(151,87,175,1)', 'rgba(191,67,55,1)'];
  colors.reverse();

  var CORPUS_SIZE_DELTA = 100000;
  var maxCorpusSize = Math.max.apply(null, unique.total);
  var uniquePerCorpusSize = [];
  for (var i = 0; i < authorNames.length; ++i) {
    var cur = [];
    for (var corpusSize = CORPUS_SIZE_DELTA; corpusSize <= unique.total[i]; corpusSize += CORPUS_SIZE_DELTA) {
      cur.push(unique[corpusSize][i] / corpusSize);
    }
    uniquePerCorpusSize.push({
      label: authorNames[i],
      data: cur,
      strokeColor: colors[i],
      pointColor: colors[i],
    });
  }
  uniquePerCorpusSize.reverse();

  var labels = [];
  for (var corpusSize = CORPUS_SIZE_DELTA; corpusSize <= maxCorpusSize; corpusSize += CORPUS_SIZE_DELTA) {
    labels.push(corpusSize);
  }

  var data = {
      labels: labels,
      datasets: uniquePerCorpusSize
  };

  var chart = new Chart(uniqueCoefCtx).Line(data, options);
</script>
