---
title: "R - Convert a Ensembl gene id to gene symbol"


date: "May 01, 2020"
layout: post
---

<script src="{{ site.url }}{{ site.baseurl }}/knitr_files/EnsembleToSymbol_files/header-attrs-2.1/header-attrs.js"></script>

<section class="main-content">
<p>One of the most frustrating things about working with ensembl gene Ids is that as humans, we like to see Gene symbols so that we can look through our lists and see our favourite genes.</p>
<p>I always found converting Ensembl Ids to Symbols in R really annoying. However, there have been a number of packages produced that have helped do this more efficienctly. I will show you how this can be done in two different ways, (1) using the <code>org.Hs.eg.db</code> and (2) <code>annotables</code>.</p>
<div id="org.hs.eg.db" class="section level2">
<h2>Org.Hs.eg.db</h2>
<div class="sourceCode" id="cb1"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb1-1"><a href="#cb1-1"></a><span class="kw">library</span>(org.Hs.eg.db)</span></code></pre></div>
<div class="sourceCode" id="cb2"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb2-1"><a href="#cb2-1"></a>infile =<span class="st"> </span><span class="kw">read.csv</span>(<span class="st">&quot;data/gene_lists.csv&quot;</span>)</span>
<span id="cb2-2"><a href="#cb2-2"></a>data =<span class="st"> </span>infile[,<span class="st">&quot;ENSEMBL&quot;</span>]</span>
<span id="cb2-3"><a href="#cb2-3"></a></span>
<span id="cb2-4"><a href="#cb2-4"></a><span class="co"># You can see that the subset data is listed as a integer, we now need to convert</span></span>
<span id="cb2-5"><a href="#cb2-5"></a><span class="co"># this to a vector to pass it into the annotation mapping</span></span>
<span id="cb2-6"><a href="#cb2-6"></a></span>
<span id="cb2-7"><a href="#cb2-7"></a>data =<span class="st"> </span><span class="kw">as.vector</span>(data)</span>
<span id="cb2-8"><a href="#cb2-8"></a></span>
<span id="cb2-9"><a href="#cb2-9"></a><span class="co"># Using the org.Hs.eg.db we set up mapping info - if you look at the documentation you</span></span>
<span id="cb2-10"><a href="#cb2-10"></a><span class="co"># can also obtain other keytypes</span></span>
<span id="cb2-11"><a href="#cb2-11"></a></span>
<span id="cb2-12"><a href="#cb2-12"></a>annots &lt;-<span class="st"> </span><span class="kw">select</span>(org.Hs.eg.db, <span class="dt">keys=</span>data, </span>
<span id="cb2-13"><a href="#cb2-13"></a>                <span class="dt">columns=</span><span class="st">&quot;SYMBOL&quot;</span>, <span class="dt">keytype=</span><span class="st">&quot;ENSEMBL&quot;</span>)</span>
<span id="cb2-14"><a href="#cb2-14"></a></span>
<span id="cb2-15"><a href="#cb2-15"></a>result &lt;-<span class="st"> </span><span class="kw">merge</span>(infile, annots, <span class="dt">by.x=</span><span class="st">&quot;ENSEMBL&quot;</span>, <span class="dt">by.y=</span><span class="st">&quot;ENSEMBL&quot;</span>)</span>
<span id="cb2-16"><a href="#cb2-16"></a></span>
<span id="cb2-17"><a href="#cb2-17"></a><span class="kw">print</span>(<span class="kw">head</span>(result))</span></code></pre></div>
<pre><code>##           ENSEMBL log2FoldChange   baseMean        padj   SYMBOL
## 1 ENSG00000000419     0.18713483 1487.61562 0.377139110     DPM1
## 2 ENSG00000000457     0.02922775  519.65035 0.927794309    SCYL3
## 3 ENSG00000000460     0.31120415  125.54166 0.380726319 C1orf112
## 4 ENSG00000000971    -0.18479425   79.10841 0.818624222      CFH
## 5 ENSG00000001036     0.39103001   53.04816 0.532827630    FUCA2
## 6 ENSG00000001084    -0.51993067 1065.13861 0.001388325     GCLC</code></pre>
</div>
<div id="annotables" class="section level2">
<h2>Annotables</h2>
<div class="sourceCode" id="cb4"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb4-1"><a href="#cb4-1"></a><span class="kw">library</span>(annotables)</span>
<span id="cb4-2"><a href="#cb4-2"></a><span class="kw">library</span>(tidyverse)</span></code></pre></div>
<div class="sourceCode" id="cb5"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb5-1"><a href="#cb5-1"></a>infile =<span class="st"> </span><span class="kw">read.csv</span>(<span class="st">&quot;data/gene_lists.csv&quot;</span>)</span>
<span id="cb5-2"><a href="#cb5-2"></a></span>
<span id="cb5-3"><a href="#cb5-3"></a>infile <span class="op">%&gt;%</span><span class="st"> </span></span>
<span id="cb5-4"><a href="#cb5-4"></a><span class="st">    </span>dplyr<span class="op">::</span><span class="kw">arrange</span>(padj) <span class="op">%&gt;%</span><span class="st"> </span></span>
<span id="cb5-5"><a href="#cb5-5"></a><span class="st">    </span><span class="kw">head</span>(<span class="dv">20</span>) <span class="op">%&gt;%</span><span class="st"> </span></span>
<span id="cb5-6"><a href="#cb5-6"></a><span class="st">    </span>dplyr<span class="op">::</span><span class="kw">inner_join</span>(grch38, <span class="dt">by =</span> <span class="kw">c</span>(<span class="st">&quot;ENSEMBL&quot;</span> =<span class="st"> &quot;ensgene&quot;</span>))</span></code></pre></div>
<pre><code>##    log2FoldChange   baseMean     padj         ENSEMBL    entrez symbol chr
## 1      -0.9193411  3487.4362 1.42e-22 ENSG00000186480      3638 INSIG1   7
## 2      -0.8311274  5338.9306 6.31e-18 ENSG00000137309      3159  HMGA1   6
## 3       1.7218713   307.1204 6.31e-18 ENSG00000214026      6150 MRPL23  11
## 4       1.7218713   307.1204 6.31e-18 ENSG00000214026 107987373 MRPL23  11
## 5       1.7218713   307.1204 6.31e-18 ENSG00000214026      6150 MRPL23  11
## 6       1.7218713   307.1204 6.31e-18 ENSG00000214026 107987373 MRPL23  11
## 7       1.3370923   506.9614 1.74e-17 ENSG00000136286     64005  MYO1G   7
## 8      -0.9048246 26303.5720 4.00e-17 ENSG00000087086      2512    FTL  19
## 9       1.3622491   349.2231 3.38e-16 ENSG00000122122     54440  SASH3   X
## 10      1.0544227  1841.3427 3.29e-14 ENSG00000116824       914    CD2   1
## 11      1.1525886   579.1535 1.18e-13 ENSG00000183735     29110   TBK1  12
## 12      1.1569100   492.1651 3.76e-13 ENSG00000133561    474344 GIMAP6   7
## 13      1.1097412  1768.2973 3.71e-12 ENSG00000171552       598 BCL2L1  20
## 14      0.8687971  2918.0275 2.18e-11 ENSG00000003402      8837  CFLAR   2
## 15      1.5528225   179.4745 3.49e-11 ENSG00000160685     51043 ZBTB7B   1
## 16     -0.8610968  4122.4284 4.47e-11 ENSG00000167996      2495   FTH1  11
## 17     -0.9730979   612.3984 2.05e-09 ENSG00000130766     83667  SESN2   1
## 18      1.1255855   284.6935 2.05e-09 ENSG00000143891    130589   GALM   2
## 19     -0.7757254  1545.5724 3.91e-09 ENSG00000162413      9903 KLHL21   1
## 20      0.9742609   657.1901 4.98e-09 ENSG00000197142     51703  ACSL5  10
## 21      0.8464779  1131.9277 8.26e-09 ENSG00000069493     29121 CLEC2D  12
## 22      1.1942058  1151.1959 1.33e-08 ENSG00000111679      5777  PTPN6  12
##        start       end strand        biotype
## 1  155297776 155310235      1 protein_coding
## 2   34236873  34246231      1 protein_coding
## 3    1947278   1984522      1 protein_coding
## 4    1947278   1984522      1 protein_coding
## 5    1947278   1984522      1 protein_coding
## 6    1947278   1984522      1 protein_coding
## 7   44962662  44979098     -1 protein_coding
## 8   48965301  48966878      1 protein_coding
## 9  129779979 129795201      1 protein_coding
## 10 116754385 116769228      1 protein_coding
## 11  64451880  64502108      1 protein_coding
## 12 150625375 150632648     -1 protein_coding
## 13  31664452  31723989     -1 protein_coding
## 14 201116104 201176687      1 protein_coding
## 15 155002630 155018522      1 protein_coding
## 16  61959718  61967660     -1 protein_coding
## 17  28259527  28282491      1 protein_coding
## 18  38665910  38741237      1 protein_coding
## 19   6590724   6614607     -1 protein_coding
## 20 112374018 112428380      1 protein_coding
## 21   9664969   9699555      1 protein_coding
## 22   6946468   6961316      1 protein_coding
##                                                                             description
## 1                             insulin induced gene 1 [Source:HGNC Symbol;Acc:HGNC:6083]
## 2                      high mobility group AT-hook 1 [Source:HGNC Symbol;Acc:HGNC:5010]
## 3               mitochondrial ribosomal protein L23 [Source:HGNC Symbol;Acc:HGNC:10322]
## 4               mitochondrial ribosomal protein L23 [Source:HGNC Symbol;Acc:HGNC:10322]
## 5               mitochondrial ribosomal protein L23 [Source:HGNC Symbol;Acc:HGNC:10322]
## 6               mitochondrial ribosomal protein L23 [Source:HGNC Symbol;Acc:HGNC:10322]
## 7                                         myosin IG [Source:HGNC Symbol;Acc:HGNC:13880]
## 8                               ferritin light chain [Source:HGNC Symbol;Acc:HGNC:3999]
## 9                   SAM and SH3 domain containing 3 [Source:HGNC Symbol;Acc:HGNC:15975]
## 10                                      CD2 molecule [Source:HGNC Symbol;Acc:HGNC:1639]
## 11                            TANK binding kinase 1 [Source:HGNC Symbol;Acc:HGNC:11584]
## 12                     GTPase, IMAP family member 6 [Source:HGNC Symbol;Acc:HGNC:21918]
## 13                                        BCL2 like 1 [Source:HGNC Symbol;Acc:HGNC:992]
## 14           CASP8 and FADD like apoptosis regulator [Source:HGNC Symbol;Acc:HGNC:1876]
## 15         zinc finger and BTB domain containing 7B [Source:HGNC Symbol;Acc:HGNC:18668]
## 16                            ferritin heavy chain 1 [Source:HGNC Symbol;Acc:HGNC:3976]
## 17                                        sestrin 2 [Source:HGNC Symbol;Acc:HGNC:20746]
## 18                             galactose mutarotase [Source:HGNC Symbol;Acc:HGNC:24063]
## 19                      kelch like family member 21 [Source:HGNC Symbol;Acc:HGNC:29041]
## 20   acyl-CoA synthetase long chain family member 5 [Source:HGNC Symbol;Acc:HGNC:16526]
## 21           C-type lectin domain family 2 member D [Source:HGNC Symbol;Acc:HGNC:14351]
## 22 protein tyrosine phosphatase, non-receptor type 6 [Source:HGNC Symbol;Acc:HGNC:9658]</code></pre>
</div>
</section>
