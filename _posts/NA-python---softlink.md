---
title: "Python - Softlink"


date: "April 07, 20217"
layout: post
---

<script src="{{ site.url }}{{ site.baseurl }}/knitr_files/Softlink_files/header-attrs-2.1/header-attrs.js"></script>

<section class="main-content">
<p>Ah, I remember the days where I used to copy data from one file location to another so I could perform another type of analysis on the same data. My files were big and my storage limited.</p>
<p>Thankfully I discovered the Symlink function in the os python module. This allows you to save space by making symbolic links to the original data, cutting down on the wasted duplication of your data files.</p>
<div class="sourceCode" id="cb1"><pre class="sourceCode python"><code class="sourceCode python"><span id="cb1-1"><a href="#cb1-1"></a><span class="im">import</span> os</span>
<span id="cb1-2"><a href="#cb1-2"></a><span class="im">import</span> glob</span>
<span id="cb1-3"><a href="#cb1-3"></a><span class="im">import</span> re</span></code></pre></div>
<div class="sourceCode" id="cb2"><pre class="sourceCode python"><code class="sourceCode python"><span id="cb2-1"><a href="#cb2-1"></a><span class="co"># Use glob.glob and wildcard to find all of your files in a certain directory (In this case I want to search for fastq files)</span></span>
<span id="cb2-2"><a href="#cb2-2"></a>infiles <span class="op">=</span> glob.glob(<span class="st">&quot;data/*fastq*&quot;</span>)</span>
<span id="cb2-3"><a href="#cb2-3"></a></span>
<span id="cb2-4"><a href="#cb2-4"></a><span class="bu">print</span> <span class="bu">len</span>(infiles)</span>
<span id="cb2-5"><a href="#cb2-5"></a><span class="co">#print(len(infile)) for python 3</span></span></code></pre></div>
<div class="sourceCode" id="cb3"><pre class="sourceCode python"><code class="sourceCode python"><span id="cb3-1"><a href="#cb3-1"></a><span class="co"># set the directory where the new file will be placed</span></span>
<span id="cb3-2"><a href="#cb3-2"></a>new_dir <span class="op">=</span> <span class="st">&quot;data/new_data/&quot;</span></span>
<span id="cb3-3"><a href="#cb3-3"></a></span>
<span id="cb3-4"><a href="#cb3-4"></a><span class="co"># Next loop through the infiles and isolate the basename</span></span>
<span id="cb3-5"><a href="#cb3-5"></a><span class="cf">for</span> infile <span class="kw">in</span> infiles:</span>
<span id="cb3-6"><a href="#cb3-6"></a>    basename <span class="op">=</span> os.path.basename(infile)</span>
<span id="cb3-7"><a href="#cb3-7"></a>    <span class="bu">print</span> basename</span>
<span id="cb3-8"><a href="#cb3-8"></a>    <span class="co"># or print(basename) for python 3</span></span>
<span id="cb3-9"><a href="#cb3-9"></a>    final_file <span class="op">=</span> new_dir <span class="op">+</span> basename</span>
<span id="cb3-10"><a href="#cb3-10"></a>    <span class="bu">print</span> final_file <span class="op">+</span> <span class="st">&quot;</span><span class="ch">\n</span><span class="st">&quot;</span></span>
<span id="cb3-11"><a href="#cb3-11"></a>    </span>
<span id="cb3-12"><a href="#cb3-12"></a>    <span class="co"># Next use symlink (softlink) functionality of os, but also check to make sure file doesnt already exist in there</span></span>
<span id="cb3-13"><a href="#cb3-13"></a>    <span class="co"># this will avoid inadvertently overwriting a previous symlink. Symlink works as follows: os.symlink(source, destination)</span></span>
<span id="cb3-14"><a href="#cb3-14"></a>    </span>
<span id="cb3-15"><a href="#cb3-15"></a>    <span class="cf">if</span> <span class="kw">not</span> os.path.exists(final_file):</span>
<span id="cb3-16"><a href="#cb3-16"></a>        os.symlink(infile, final_file)</span>
<span id="cb3-17"><a href="#cb3-17"></a>    <span class="cf">else</span>:</span>
<span id="cb3-18"><a href="#cb3-18"></a>        <span class="bu">print</span> <span class="st">&quot;sample symlink already exists&quot;</span></span></code></pre></div>
<p>output: data.fastq.gz data/new_data/data.fastq.gz</p>
<p>other_data.fastq.gz data/new_data/other_data.fastq.gz</p>
</section>
