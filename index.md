<html>
<head>
<meta charset="utf-8" />
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<meta name="generator" content="pandoc" />
<meta name="author" content="Webert Saturnino Pinto" />
<title>Cálculo de índices Balanceados de Kenworthy (B) utlizando o R</title>
<meta name="viewport" content="width=device-width, initial-scale=1" />
<style type="text/css">code{white-space: pre;}</style>
<style type="text/css">
div.sourceCode { overflow-x: auto; }
table.sourceCode, tr.sourceCode, td.lineNumbers, td.sourceCode {
  margin: 0; padding: 0; vertical-align: baseline; border: none; }
table.sourceCode { width: 100%; line-height: 100%; }
td.lineNumbers { text-align: right; padding-right: 4px; padding-left: 4px; color: #aaaaaa; border-right: 1px solid #aaaaaa; }
td.sourceCode { padding-left: 5px; }
code > span.kw { color: #0000ff; } /* Keyword */
code > span.ch { color: #008080; } /* Char */
code > span.st { color: #008080; } /* String */
code > span.co { color: #008000; } /* Comment */
code > span.ot { color: #ff4000; } /* Other */
code > span.al { color: #ff0000; } /* Alert */
code > span.er { color: #ff0000; font-weight: bold; } /* Error */
code > span.wa { color: #008000; font-weight: bold; } /* Warning */
code > span.cn { } /* Constant */
code > span.sc { color: #008080; } /* SpecialChar */
code > span.vs { color: #008080; } /* VerbatimString */
code > span.ss { color: #008080; } /* SpecialString */
code > span.im { } /* Import */
code > span.va { } /* Variable */
code > span.cf { color: #0000ff; } /* ControlFlow */
code > span.op { } /* Operator */
code > span.bu { } /* BuiltIn */
code > span.ex { } /* Extension */
code > span.pp { color: #ff4000; } /* Preprocessor */
code > span.do { color: #008000; } /* Documentation */
code > span.an { color: #008000; } /* Annotation */
code > span.cv { color: #008000; } /* CommentVar */
code > span.at { } /* Attribute */
code > span.in { color: #008000; } /* Information */
</style>
<style type="text/css">
  pre:not([class]) {
    background-color: white;
  }
</style>


<style type="text/css">
h1 {
  font-size: 34px;
}
h1.title {
  font-size: 38px;
}
h2 {
  font-size: 30px;
}
h3 {
  font-size: 24px;
}
h4 {
  font-size: 18px;
}
h5 {
  font-size: 16px;
}
h6 {
  font-size: 12px;
}
.table th:not([align]) {
  text-align: left;
}
</style>


</head>

<body>

<style type="text/css">
.main-container {
  max-width: 940px;
  margin-left: auto;
  margin-right: auto;
}
code {
  color: inherit;
  background-color: rgba(0, 0, 0, 0.04);
}
img {
  max-width:100%;
  height: auto;
}
.tabbed-pane {
  padding-top: 12px;
}
button.code-folding-btn:focus {
  outline: none;
}
</style>



<div class="container-fluid main-container">

<!-- tabsets -->
<script>
$(document).ready(function () {
  window.buildTabsets("TOC");
});
</script>

<!-- code folding -->






<div class="fluid-row" id="header">



<h1 class="title toc-ignore">Cálculo de índices Balanceados de Kenworthy (B)</h1>
<h4 class="author"><em>Webert Saturnino Pinto</em></h4>
<h4 class="date"><em>24 de junho de 2017</em></h4>

</div>


<style>
body {
text-align: justify}
</style>
<div id="introducao" class="section level3">
<h3>Introdução</h3>
<p>De acordo com MARTINEZ et al. (1999), os ídices balanceados de Kenworthy, propostos por KENWORTHY(1961), permitem avaliar o estado nutricinal como percentagem da concentração de determinado nutriente em relação à norma. A vantagem deste método em relação à outros é o fato deste considerar o coeficiente de variação para cada nutriente na população de onde se obteve a norma. Para o cálculo dos coeficientes, procede-se destintamente quando a concentração do nutriente na população teste for menor ou maior que a norma:</p>
<ol style="list-style-type: lower-alpha">
<li><p>Y_am &gt; Y_ref</p>
<p>I = (P-100)CV/100</p>
<p>B = P-I</p></li>
<li><p>Y_am &lt; Y_ref</p>
<p>I = (100-P)CV/100</p>
<p>B = P+I</p></li>
</ol>
<p>A interpretação dos resultados é feita de acordo com a colocação dos valores em relação às faixas:</p>
<ol style="list-style-type: decimal">
<li>Faixa de deficiência: 17 a 50 %</li>
<li>Faixa marginal (abaixo do normal): 50 a 83 %</li>
<li>Faixa adequada (normal): 83 a 117 %</li>
<li>Faixa elevada (acima do normal): 117 a 150 %</li>
<li>Faixa de excesso: 150 a 183 %</li>
</ol>
</div>
<div id="bibliotecas-adicionais-utilizadas" class="section level3">
<h3>Bibliotecas adicionais utilizadas</h3>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class="kw">library</span>(data.table)
<span class="kw">library</span>(dplyr)
<span class="kw">library</span>(matrixStats)
<span class="kw">library</span>(ggplot2)</code></pre></div>
</div>
<div id="lendo-a-base-de-dados" class="section level3">
<h3>Lendo a base de dados</h3>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class="co">#Arquivo com os dados de referência, para cálculo da norma:</span>

d.ref &lt;-<span class="st"> </span><span class="kw">data.frame</span>(<span class="kw">read.csv2</span>(<span class="st">&quot;dados_ref_cafe.csv&quot;</span>)) 

<span class="kw">print</span>(d.ref)</code></pre></div>
<pre><code>##    amostra    N    P    K    S   Ca   Mg Cu  Mn  Fe Zn  B
## 1     am_1 3.00 0.20 2.30 0.18 2.25 0.35 15 180 130 13 75
## 2     am_2 2.85 0.15 2.00 0.16 2.20 0.35 14 113 127  9 60
## 3     am_3 2.37 0.14 2.10 0.20 2.50 0.40 10 127 155  5 63
## 4     am_4 2.56 0.16 2.50 0.22 2.40 0.20 12 190 140  8 80
## 5     am_5 2.70 0.15 2.80 0.28 2.10 0.30 15 205 100 14 77
## 6     am_6 3.50 0.14 2.50 0.19 2.20 0.25  9 137 125 16 58
## 7     am_7 2.89 0.13 2.20 0.16 3.00 0.30 16 113 130 13 75
## 8     am_8 3.01 0.27 2.33 0.30 2.35 0.35 20 148 135 15 80
## 9     am_9 3.60 0.25 2.50 0.15 2.40 0.25 12 136 130 10 66
## 10   am_10 2.30 0.10 3.00 0.20 2.85 0.20 15 144 150 12 60</code></pre>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class="co">#Arquivo com os teores foliares da amostra sob avaliação:</span>

d.amostra &lt;-<span class="st"> </span><span class="kw">data.frame</span>(<span class="kw">read.csv2</span>(<span class="st">&quot;dados_am_cafe.csv&quot;</span>))
<span class="kw">print</span>(d.amostra)</code></pre></div>
<pre><code>##         nut    N    P    K   S   Ca   Mg    Cu     Mn     Fe    Zn  B
## 1 t_amostra 2.41 0.23 1.42 0.2 1.18 0.22 11.49 207.03 141.15 13.23 60</code></pre>
</div>
<div id="calculando-os-valores-das-medias-e-variancias-da-populacao-de-referencia" class="section level3">
<h3>Calculando os valores das médias e variâncias da população de referência</h3>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">d.ref.media &lt;-<span class="kw">data.frame</span>(<span class="dt">y_ref =</span> <span class="kw">sapply</span>(d.ref[<span class="dv">2</span>:<span class="dv">10</span>,<span class="dv">2</span>:<span class="dv">12</span>], mean))
d.ref.variancia &lt;-<span class="kw">data.frame</span>(<span class="dt">variancia =</span> <span class="kw">sapply</span>(d.ref[<span class="dv">2</span>:<span class="dv">10</span>,<span class="dv">2</span>:<span class="dv">12</span>], sd))
d.ref.media.var &lt;-<span class="st"> </span><span class="kw">cbind.data.frame</span>(d.ref.media, d.ref.variancia)</code></pre></div>
</div>
<div id="calculando-o-coeficiente-percentual-de-variacao-cv-da-populacao-de-referencia-norma" class="section level3">
<h3>Calculando o coeficiente percentual de variação (CV) da população de referência (norma)</h3>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">d.ref.media.var$cv &lt;-<span class="st"> </span>(d.ref.media.var$variancia/d.ref.media.var$y_ref)*<span class="dv">100</span></code></pre></div>
</div>
<div id="criando-um-data-frame-com-os-valores-referencia-e-de-amostra" class="section level3">
<h3>Criando um data frame com os valores referência e de amostra</h3>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">d.kw &lt;-<span class="st"> </span><span class="kw">data.frame</span>(d.ref.media.var)
y &lt;-<span class="st"> </span><span class="kw">as.data.frame</span>(<span class="kw">t</span>(d.amostra))
Y&lt;-<span class="st"> </span><span class="kw">data.frame</span>(<span class="dt">v =</span> y[<span class="dv">2</span>:<span class="dv">12</span>,])
d.kw$y_am &lt;-<span class="st"> </span><span class="kw">as.numeric</span>(<span class="kw">as.character</span>(Y$v))
d.kw$y_ref &lt;-<span class="st">  </span><span class="kw">as.numeric</span>(<span class="kw">as.character</span>(d.kw$y_ref))</code></pre></div>
</div>
<div id="calculando-os-parametros-i-e-p" class="section level3">
<h3>Calculando os parâmetros I e P</h3>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">d.kw$P &lt;-<span class="st"> </span>(d.kw$y_am/d.kw$y_ref)*<span class="dv">100</span>

d.kw$I &lt;-<span class="st"> </span><span class="kw">ifelse</span>(d.kw$y_am &gt;<span class="st"> </span>d.kw$y_ref, (d.kw$P<span class="dv">-100</span>)*(d.kw$cv/<span class="dv">100</span>), (<span class="dv">100</span>-d.kw$P)*(d.kw$cv/<span class="dv">100</span>) )</code></pre></div>
</div>
<div id="calculando-os-indices-balanceados-de-kenworthy" class="section level3">
<h3>Calculando os índices balanceados de Kenworthy</h3>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">d.kw$B &lt;-<span class="st"> </span><span class="kw">ifelse</span>(d.kw$y_am &gt;<span class="st"> </span>d.kw$y_ref, d.kw$P-<span class="st"> </span>d.kw$I, d.kw$P +<span class="st"> </span>d.kw$I)</code></pre></div>
</div>
<div id="interpretando-os-valores-obtidos-para-os-indices" class="section level3">
<h3>Interpretando os valores obtidos para os índices</h3>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">d.kw$interpretacao &lt;-<span class="st"> </span><span class="kw">ifelse</span>(d.kw$B &gt;=<span class="st"> </span><span class="dv">17</span> &amp;<span class="st"> </span>d.kw$B &lt;<span class="st"> </span><span class="dv">50</span>, <span class="st">&quot;Deficiência&quot;</span>,
                             <span class="kw">ifelse</span>(d.kw$B &gt;=<span class="st"> </span><span class="dv">50</span> &amp;<span class="st"> </span>d.kw$B &lt;<span class="st"> </span><span class="dv">83</span>, <span class="st">&quot;Abaixo do normal&quot;</span>,
                                    <span class="kw">ifelse</span>(d.kw$B &gt;=<span class="st"> </span><span class="dv">83</span> &amp;<span class="st"> </span>d.kw$B &lt;<span class="st"> </span><span class="dv">117</span>,<span class="st">&quot;Normal&quot;</span>, 
                                           <span class="kw">ifelse</span>(d.kw$B &gt;=<span class="st"> </span><span class="dv">117</span> &amp;<span class="st"> </span>d.kw$B &lt;<span class="st"> </span><span class="dv">150</span>,<span class="st">&quot;Acima do normal&quot;</span>, <span class="st">&quot;Excesso&quot;</span>))))</code></pre></div>
</div>
<div id="reordenando-os-indices-dos-nutrintes-em-funcao-do-coeficiente-b" class="section level3">
<h3>Reordenando os índices dos nutrintes em funcão do coeficiente B</h3>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">d.ikw &lt;-<span class="st"> </span><span class="kw">data.table</span>(<span class="dt">nutriente =</span> <span class="kw">row.names</span>(d.kw), <span class="dt">B =</span> d.kw$B, <span class="dt">Interpretacao =</span> d.kw$interpretacao) %&gt;%<span class="st"> </span><span class="kw">transform</span>(<span class="dt">nutriente =</span> <span class="kw">reorder</span>(nutriente, -B))
<span class="kw">print</span>(d.ikw)</code></pre></div>
<pre><code>##     nutriente         B    Interpretacao
##  1:         N  86.65019           Normal
##  2:         P 125.67179  Acima do normal
##  3:         K  63.80107 Abaixo do normal
##  4:         S  97.59643           Normal
##  5:        Ca  54.65283 Abaixo do normal
##  6:        Mg  81.90898 Abaixo do normal
##  7:        Cu  87.98197           Normal
##  8:        Mn 132.74323  Acima do normal
##  9:        Fe 105.78239           Normal
## 10:        Zn 111.41118           Normal
## 11:         B  88.93508           Normal</code></pre>
</div>
<div id="plotando-os-indices-balanceados-para-cada-nutriente" class="section level3">
<h3>Plotando os índices balanceados para cada nutriente</h3>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">graf_ikw &lt;-<span class="st"> </span><span class="kw">ggplot</span>(d.ikw, <span class="kw">aes</span>(nutriente,B))
graf_ikw +<span class="st"> </span><span class="kw">geom_col</span>()+<span class="st"> </span>
<span class="st">  </span><span class="kw">ylab</span>(<span class="st">&quot;B(%)&quot;</span>) +<span class="st"> </span>
<span class="st">  </span><span class="kw">geom_hline</span>(<span class="dt">yintercept =</span> <span class="dv">17</span>)+
<span class="st">    </span><span class="kw">annotate</span>(<span class="dt">geom=</span><span class="st">&quot;text&quot;</span>, <span class="dt">label=</span> <span class="st">&quot;Deficiente&quot;</span>, <span class="dt">x=</span><span class="dv">1</span>, <span class="dt">y=</span><span class="fl">32.5</span>, <span class="dt">vjust=</span><span class="dv">0</span>, <span class="dt">hjust =</span> -<span class="dv">4</span>)+
<span class="st">  </span><span class="kw">geom_hline</span>(<span class="dt">yintercept =</span> <span class="dv">50</span>)+
<span class="st">    </span><span class="kw">annotate</span>(<span class="dt">geom=</span><span class="st">&quot;text&quot;</span>, <span class="dt">label=</span> <span class="st">&quot;Abaixo do normal&quot;</span>, <span class="dt">x=</span><span class="dv">1</span>, <span class="dt">y=</span><span class="fl">66.5</span>, <span class="dt">vjust=</span><span class="dv">0</span>, <span class="dt">hjust =</span> -<span class="dv">2</span>)+
<span class="st">  </span><span class="kw">geom_hline</span>(<span class="dt">yintercept =</span> <span class="dv">83</span>)+
<span class="st">    </span><span class="kw">annotate</span>(<span class="dt">geom=</span><span class="st">&quot;text&quot;</span>, <span class="dt">label=</span> <span class="st">&quot;Normal&quot;</span>, <span class="dt">x=</span><span class="dv">1</span>, <span class="dt">y=</span><span class="dv">100</span>, <span class="dt">vjust=</span><span class="dv">0</span>, <span class="dt">hjust =</span> -<span class="fl">5.25</span>)+
<span class="st">  </span><span class="kw">geom_hline</span>(<span class="dt">yintercept =</span> <span class="dv">117</span>)+
<span class="st">    </span><span class="kw">annotate</span>(<span class="dt">geom=</span><span class="st">&quot;text&quot;</span>, <span class="dt">label=</span> <span class="st">&quot;Acima do normal&quot;</span>, <span class="dt">x=</span><span class="dv">1</span>, <span class="dt">y=</span><span class="fl">133.5</span>, <span class="dt">vjust=</span><span class="dv">0</span>, <span class="dt">hjust =</span> -<span class="dv">2</span>)+
<span class="st">  </span><span class="kw">geom_hline</span>(<span class="dt">yintercept =</span> <span class="dv">150</span>)+
<span class="st">    </span><span class="kw">annotate</span>(<span class="dt">geom=</span><span class="st">&quot;text&quot;</span>, <span class="dt">label=</span> <span class="st">&quot;Excesso&quot;</span>, <span class="dt">x=</span><span class="dv">1</span>, <span class="dt">y=</span><span class="dv">150</span>, <span class="dt">vjust=</span>-<span class="fl">0.5</span>, <span class="dt">hjust =</span> -<span class="fl">4.3</span>)</code></pre></div>
<p><img src="data:image/png;base64,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" width="576" /></p>
</div>
<div id="fontes-consultadas-e-referenciadas" class="section level3">
<h3>Fontes consultadas e referenciadas</h3>
<p>KENWORTHY, A. L. Interpreting the balance of nutrient-elements in leaves of fruit trees. In: Reuther W. Plant analysis and fertilizers problems. Washington: American Institute of Biological Science, 1961. p.28-23.</p>
<p>MARTINEZ, H. E. P.; CARVALHO, J. G.; SOUZA, R. B. Diagnose Foliar. In: RIBEIRO, A. C.; GUIMARÃES, P. T. G.; ALVAREZ V., V. H. (Org.). . Recomendações para o uso de corretivos e fertilizantes em Minas Gerais - 5° Aproximação. Viçosa: [s.n.], 1999. p. 359.</p>
<p>ggplot2 barplots : Quick start guide - R software and data visualization. Disponível em: <a href="http://www.sthda.com/english/wiki/ggplot2-barplots-quick-start-guide-r-software-and-data-visualization" class="uri">http://www.sthda.com/english/wiki/ggplot2-barplots-quick-start-guide-r-software-and-data-visualization</a>. Último acesso: 26/06/2017.</p>
<p>Find the Standard deviation for a vector, matrix, or data.frame - do not return error if there are no cases. Disponível em:<a href="https://www.personality-project.org/r/html/SD.html" class="uri">https://www.personality-project.org/r/html/SD.html</a>. Último acesso: 26/06/2017.</p>
<p>Disponível em:<a href="http://www.statmethods.net/management/subset.html" class="uri">http://www.statmethods.net/management/subset.html</a>. Último acesso: 26/06/2017.</p>
<p>Disponível em:<a href="https://s3.amazonaws.com/content.udacity-data.com/courses/gt-cse6242/recommended+reading/ggplot2-book.pdf" class="uri">https://s3.amazonaws.com/content.udacity-data.com/courses/gt-cse6242/recommended+reading/ggplot2-book.pdf</a>. Último acesso: 26/06/2017.</p>
<p>Disponível em:<a href="https://stackoverflow.com/questions/39178740/ggplot2-reorder-bars-in-barplot-from-highest-to-lowest" class="uri">https://stackoverflow.com/questions/39178740/ggplot2-reorder-bars-in-barplot-from-highest-to-lowest</a>. Último acesso: 26/06/2017.</p>
</div>



</div>

<script>

// add bootstrap table styles to pandoc tables
function bootstrapStylePandocTables() {
  $('tr.header').parent('thead').parent('table').addClass('table table-condensed');
}
$(document).ready(function () {
  bootstrapStylePandocTables();
});


</script>

<!-- dynamically load mathjax for compatibility with self-contained -->
<script>
  (function () {
    var script = document.createElement("script");
    script.type = "text/javascript";
    script.src  = "https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML";
    document.getElementsByTagName("head")[0].appendChild(script);
  })();
</script>

</body>
</html>
