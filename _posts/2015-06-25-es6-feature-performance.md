---
layout: post
title: ES6 Feature Performance
date: 2015-06-25 19:25:06.000000000 -05:00
categories:
- Dev
tags:
- ES6
- Performance
- Web Development
excerpt_separator: <!--more-->
---

With ES6 features landing quickly in native browsers and readily available for use through Babel and Traceur, it seemed like it was time to look not just at <a href="https://kangax.github.io/compat-table/es6/">support</a>, but also the performance impact of using these features under the current implementations.

While there is great promise for the future, the picture of ES6 feature performance today is very muddled and depends on the specific feature being used. Some such as bindings and simple arrow/destructuring are ready for use today, others such as generators and tagged template strings might require analysis before using.

<!--more-->

The standard warnings of premature optimization and recommendations to profile your own code apply to the comments here. These tests are very micro in their scope and might not be representative of your particular use case. It's also possible that the ES6 version is fast enough for your use case and is not worth additional time spent refactoring to a more complicated but faster implementation.

## Results

The results below are a snapshot from when this post was written. <a href="http://kpdecker.github.io/six-speed/">kpdecker.github.io/six-speed/</a> has the most recent results.

### Arrow Function
Arrow functions invocation has little performance impact under transpilers. Their native implementation under Firefox is 40-70x slower for calls than the equivalent ES5 operation. Internet Explorer's performance is approximately that of ES5.

Arrow function declaration on the other hand is slightly slower than the most optimized ES5 implementation, under most environments. Here too Firefox's implementation shows a large performance hit and IE shows a slight performance hit.

<table>
<thead>
<tr>
<td colspan="2"></td>
<th colspan="2">node</th>
<th colspan="3">chrome</th>
<th colspan="3">firefox</th>
<th colspan="2">internet explorer</th>
<th colspan="1">safari</th>
</tr>
<tr>
<td colspan="2"></td>
<th>0.10.39</th>
<th>2.3.0</th>
<th>43</th>
<th>44</th>
<th>45</th>
<th>38</th>
<th>39</th>
<th>40</th>
<th>11</th>
<th>12</th>
<th>8</th>
</tr>
</thead>
<tbody>
<tr id="user-content-test-arrow">
<th rowspan="4">
          arrow<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/arrow">tests</a>
        </th>
<th>babel</th>
<td>1.3x slower</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>1.2x faster</td>
<td>Identical</td>
</tr>
<tr>
<th>traceur</th>
<td>1.3x slower</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>1.3x faster</td>
<td>Identical</td>
</tr>
<tr>
<th>es5-bind</th>
<td>10x slower</td>
<td>15x slower</td>
<td>19x slower</td>
<td>14x slower</td>
<td>20x slower</td>
<td>6x slower</td>
<td>6x slower</td>
<td>7x slower</td>
<td>4x slower</td>
<td>2.6x slower</td>
<td>3x slower</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td>38x slower</td>
<td>41x slower</td>
<td>52x slower</td>
<td></td>
<td>Identical</td>
<td></td>
</tr>
<tr id="user-content-test-arrow-args">
<th rowspan="3">
          arrow-args<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/arrow-args">tests</a>
        </th>
<th>babel</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>1.2x faster</td>
<td>Identical</td>
</tr>
<tr>
<th>traceur</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>1.2x faster</td>
<td>Identical</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td>2.9x slower</td>
<td></td>
<td></td>
<td></td>
<td>64x slower</td>
<td>89x slower</td>
<td>68x slower</td>
<td></td>
<td>1.3x faster</td>
<td></td>
</tr>
<tr id="user-content-test-arrow-declare">
<th rowspan="3">
          arrow-declare<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/arrow-declare">tests</a>
        </th>
<th>babel</th>
<td>1.3x slower</td>
<td>1.3x slower</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>8x slower</td>
<td>10x slower</td>
<td>13x slower</td>
<td>2.3x slower</td>
<td>1.6x slower</td>
<td>1.4x slower</td>
</tr>
<tr>
<th>traceur</th>
<td>1.3x slower</td>
<td>1.3x slower</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>5x slower</td>
<td>7x slower</td>
<td>8x slower</td>
<td>2.3x slower</td>
<td>1.4x slower</td>
<td>1.4x slower</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td>Identical</td>
<td></td>
<td></td>
<td></td>
<td>53x slower</td>
<td>78x slower</td>
<td>78x slower</td>
<td></td>
<td>1.3x slower</td>
<td></td>
</tr>
</tbody>
</table>
<p>Issues:</p>
<ul>
<li><a href="https://bugzilla.mozilla.org/show_bug.cgi?id=1177518">Firefox Native Implementation</a></li>
</ul>
### Classes
<p>With classes we start to see some differences in behaviors. Traceur and the V8 native implementation operate at partity with the ES5 tests when looking at instantiation. Babel's implementation does suffer a 1.5-60x performance hit for the operations tested. When compiling using Babel's loose mode, the hit is lessened to 8x.</p>
<p>The <code>super</code> keyword has some fairly large performance issues under all implementations, with the best case being 3x slower and the worst case being 60x slower than the respective baselines. V8's native implementation also sees a 15-20x performance hit.</p>
<p>Babel's loose implementation of <code>super</code> is akin to that of the ES5 implementation, utilizing <code>C.prototype.bar.call(this)</code> rather than the slower <code>getPrototypeOf</code> lookup operation that while more accurate technically, incurs an additional cost. This is controlled by the <code>es6.classes</code> parameter but Babel's authors cite a number of <a href="https://babeljs.io/docs/advanced/loose/#es6-classes">warnings</a> with this flag that may impact compatibility when migrating code to native implementations.</p>
<table>
<thead>
<tr>
<td colspan="2"></td>
<th colspan="2">node</th>
<th colspan="3">chrome</th>
<th colspan="3">firefox</th>
<th colspan="2">internet explorer</th>
<th colspan="1">safari</th>
</tr>
<tr>
<td colspan="2"></td>
<th>0.10.39</th>
<th>2.3.0</th>
<th>43</th>
<th>44</th>
<th>45</th>
<th>38</th>
<th>39</th>
<th>40</th>
<th>11</th>
<th>12</th>
<th>8</th>
</tr>
</thead>
<tbody>
<tr id="user-content-test-classes">
<th rowspan="4">
          classes<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/classes">tests</a>
        </th>
<th>babel</th>
<td>2.3x slower</td>
<td>1.5x slower</td>
<td>1.3x slower</td>
<td>1.4x slower</td>
<td>1.3x slower</td>
<td>27x slower</td>
<td>26x slower</td>
<td>30x slower</td>
<td>1.4x slower</td>
<td>1.5x slower</td>
<td>Identical</td>
</tr>
<tr>
<th>babel-loose</th>
<td>2.3x slower</td>
<td>1.5x slower</td>
<td>1.3x slower</td>
<td>1.4x slower</td>
<td>1.3x slower</td>
<td>6x slower</td>
<td>6x slower</td>
<td>8x slower</td>
<td>1.3x slower</td>
<td>2.4x slower</td>
<td>Identical</td>
</tr>
<tr>
<th>traceur</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>1.5x slower</td>
<td>1.7x slower</td>
<td>1.8x slower</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr id="user-content-test-super">
<th rowspan="4">
          super<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/super">tests</a>
        </th>
<th>babel</th>
<td>24x slower</td>
<td>60x slower</td>
<td>52x slower</td>
<td>48x slower</td>
<td>45x slower</td>
<td>60x slower</td>
<td>62x slower</td>
<td>61x slower</td>
<td>24x slower</td>
<td>24x slower</td>
<td>16x slower</td>
</tr>
<tr>
<th>babel-loose</th>
<td>2.0x slower</td>
<td>1.7x slower</td>
<td>1.7x slower</td>
<td>1.7x slower</td>
<td>1.7x slower</td>
<td>5x slower</td>
<td>5x slower</td>
<td>5x slower</td>
<td>3x slower</td>
<td>3.0x slower</td>
<td>1.2x slower</td>
</tr>
<tr>
<th>traceur</th>
<td>12x slower</td>
<td>26x slower</td>
<td>18x slower</td>
<td>18x slower</td>
<td>18x slower</td>
<td>26x slower</td>
<td>30x slower</td>
<td>27x slower</td>
<td>10x slower</td>
<td>12x slower</td>
<td>11x slower</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td>14x slower</td>
<td>19x slower</td>
<td>21x slower</td>
<td>18x slower</td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table>
<p>Issues:</p>
<ul>
<li><a href="https://code.google.com/p/v8/issues/detail?id=3759">V8</a></li>
<li><a href="https://bugzilla.mozilla.org/show_bug.cgi?id=1167472">Firefox Class Performance</a></li>
<li><a href="https://bugzilla.mozilla.org/show_bug.cgi?id=1169745">Firefox Super Property Performance</a></li>
<li><a href="https://bugzilla.mozilla.org/show_bug.cgi?id=1169746">Firefox Super Call Performance</a></li>
</ul>
<h3>
<a id="user-content-enhanced-object-literals" class="anchor" href="#enhanced-object-literals" aria-hidden="true"><span class="octicon octicon-link"></span></a>Enhanced Object Literals</h3>
<p>Object literal extensions generally provide an overhead of up to 147x the baseline. Under the transpiler implementations this is due to the use of <code>defineProperty</code> rather than the much more optimized field assignment. This is done to bullet proof code from potential edge cases discussed <a href="https://github.com/babel/babel/issues/357">here</a>. Loose mode is effectively the same as the ES5 implementation as of <a href="https://github.com/babel/babel/issues/1820">5.6.7</a></p>
<table>
<thead>
<tr>
<td colspan="2"></td>
<th colspan="2">node</th>
<th colspan="3">chrome</th>
<th colspan="3">firefox</th>
<th colspan="2">internet explorer</th>
<th colspan="1">safari</th>
</tr>
<tr>
<td colspan="2"></td>
<th>0.10.39</th>
<th>2.3.0</th>
<th>43</th>
<th>44</th>
<th>45</th>
<th>38</th>
<th>39</th>
<th>40</th>
<th>11</th>
<th>12</th>
<th>8</th>
</tr>
</thead>
<tbody>
<tr id="user-content-test-object-literal-ext">
<th rowspan="4">
          object-literal-ext<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/object-literal-ext">tests</a>
        </th>
<th>babel</th>
<td>4x slower</td>
<td>60x slower</td>
<td>72x slower</td>
<td>71x slower</td>
<td>80x slower</td>
<td>1.8x slower</td>
<td>1.6x slower</td>
<td>1.6x slower</td>
<td>2.6x slower</td>
<td>2.0x slower</td>
<td>2.5x slower</td>
</tr>
<tr>
<th>babel-loose</th>
<td>Identical</td>
<td>12x slower</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
</tr>
<tr>
<th>traceur</th>
<td>8x slower</td>
<td>120x slower</td>
<td>143x slower</td>
<td>122x slower</td>
<td>144x slower</td>
<td>3x slower</td>
<td>3x slower</td>
<td>2.9x slower</td>
<td>5x slower</td>
<td>4x slower</td>
<td>4x slower</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td>25x slower</td>
<td></td>
<td>23x slower</td>
<td>26x slower</td>
<td>1.4x slower</td>
<td>1.5x slower</td>
<td>1.3x slower</td>
<td></td>
<td>Identical</td>
<td></td>
</tr>
</tbody>
</table>
<p>Issues:</p>
<ul>
<li><a href="https://github.com/babel/babel/pull/1830">Babel Optimization</a></li>
<li><a href="https://code.google.com/p/v8/issues/detail?id=4246">V8</a></li>
</ul>
<h3>
<a id="user-content-template-strings" class="anchor" href="#template-strings" aria-hidden="true"><span class="octicon octicon-link"></span></a>Template Strings</h3>
<p>Template strings are a mixed bag. In the basic form, transpilers are able to hit parity with the baseline implementation under most environments. The native implementations are hit or miss. Under Chrome they execute at half the speed and under Firefox up to 650x slower.</p>
<p>Tagged template strings unfortunately do not have such a nice outlook. Their performance ranged from 2x slower for IE's native implementation to 2000x slower for Babel's implementation under Firefox. Babel's loose implementation (<code>es6.templateLiterals</code>) lessens much of the overhead of this operation, at the cost of not having a fully compliant <code>String.raw</code> implementation.</p>
<p>Issues:</p>
<ul>
<li><a href="https://code.google.com/p/chromium/issues/detail?id=504212">Chrome</a></li>
<li><a href="https://bugzilla.mozilla.org/show_bug.cgi?id=1177318">Firefox</a></li>
<li><a href="https://github.com/babel/babel/issues/971#issuecomment-115344157">Babel</a></li>
</ul>
<table>
<thead>
<tr>
<td colspan="2"></td>
<th colspan="2">node</th>
<th colspan="3">chrome</th>
<th colspan="3">firefox</th>
<th colspan="2">internet explorer</th>
<th colspan="1">safari</th>
</tr>
<tr>
<td colspan="2"></td>
<th>0.10.39</th>
<th>2.3.0</th>
<th>43</th>
<th>44</th>
<th>45</th>
<th>38</th>
<th>39</th>
<th>40</th>
<th>11</th>
<th>12</th>
<th>8</th>
</tr>
</thead>
<tbody>
<tr id="user-content-test-template_string">
<th rowspan="3">
          template_string<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/template_string">tests</a>
        </th>
<th>babel</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>1.5x faster</td>
<td>1.4x faster</td>
<td>Identical</td>
</tr>
<tr>
<th>traceur</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td>Identical</td>
<td>2.0x slower</td>
<td>2.0x slower</td>
<td>1.9x slower</td>
<td>627x slower</td>
<td>622x slower</td>
<td>591x slower</td>
<td></td>
<td>1.3x faster</td>
<td></td>
</tr>
<tr id="user-content-test-template_string_tag">
<th rowspan="4">
          template_string_tag<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/template_string_tag">tests</a>
        </th>
<th>babel</th>
<td>567x slower</td>
<td>578x slower</td>
<td>722x slower</td>
<td>755x slower</td>
<td>820x slower</td>
<td>2395x slower</td>
<td>2349x slower</td>
<td>2028x slower</td>
<td>90x slower</td>
<td>82x slower</td>
<td>61x slower</td>
</tr>
<tr>
<th>babel-loose</th>
<td>2.0x slower</td>
<td>1.6x slower</td>
<td>1.7x slower</td>
<td>1.6x slower</td>
<td>1.8x slower</td>
<td>94x slower</td>
<td>87x slower</td>
<td>84x slower</td>
<td>1.4x slower</td>
<td>1.4x slower</td>
<td>2.0x slower</td>
</tr>
<tr>
<th>traceur</th>
<td>7x slower</td>
<td>13x slower</td>
<td>17x slower</td>
<td>13x slower</td>
<td>16x slower</td>
<td>346x slower</td>
<td>307x slower</td>
<td>261x slower</td>
<td>13x slower</td>
<td>11x slower</td>
<td>8x slower</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td>8x slower</td>
<td>9x slower</td>
<td>13x slower</td>
<td>9x slower</td>
<td>68x slower</td>
<td>64x slower</td>
<td>59x slower</td>
<td></td>
<td>Identical</td>
<td></td>
</tr>
</tbody>
</table>
<h3>
<a id="user-content-destructuring" class="anchor" href="#destructuring" aria-hidden="true"><span class="octicon octicon-link"></span></a>Destructuring</h3>
<p>For destructuring, the average use case effectively matches that of the ES5 counterpart. Unfortunately complex use cases, particularly those around array destructuring, often have large performance overhead. Under Babel an unoptimized helper is used to access the data and under Traceur an entire iterator structure is created, both of which provide fairly substantial memory and CPU overhead over the simple array accessor logic that hand coded ES5 can utilize. Loose mode is effectively the same as the ES5 implementation.</p>
<table>
<thead>
<tr>
<td colspan="2"></td>
<th colspan="2">node</th>
<th colspan="3">chrome</th>
<th colspan="3">firefox</th>
<th colspan="2">internet explorer</th>
<th colspan="1">safari</th>
</tr>
<tr>
<td colspan="2"></td>
<th>0.10.39</th>
<th>2.3.0</th>
<th>43</th>
<th>44</th>
<th>45</th>
<th>38</th>
<th>39</th>
<th>40</th>
<th>11</th>
<th>12</th>
<th>8</th>
</tr>
</thead>
<tbody>
<tr id="user-content-test-destructuring">
<th rowspan="4">
          destructuring<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/destructuring">tests</a>
        </th>
<th>babel</th>
<td>1.8x slower</td>
<td>1.4x slower</td>
<td>1.4x slower</td>
<td>1.2x slower</td>
<td>1.3x slower</td>
<td>24x slower</td>
<td>22x slower</td>
<td>26x slower</td>
<td>3x slower</td>
<td>5x slower</td>
<td>2.2x slower</td>
</tr>
<tr>
<th>babel-loose</th>
<td>1.2x faster</td>
<td>Identical</td>
<td>Identical</td>
<td>1.2x faster</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
</tr>
<tr>
<th>traceur</th>
<td>26x slower</td>
<td>13x slower</td>
<td>12x slower</td>
<td>10x slower</td>
<td>11x slower</td>
<td>163x slower</td>
<td>152x slower</td>
<td>176x slower</td>
<td>57x slower</td>
<td>25x slower</td>
<td>8x slower</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td>170x slower</td>
<td>148x slower</td>
<td>185x slower</td>
<td></td>
<td></td>
<td>Identical</td>
</tr>
<tr id="user-content-test-destructuring-simple">
<th rowspan="3">
          destructuring-simple<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/destructuring-simple">tests</a>
        </th>
<th>babel</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>10x slower</td>
<td>1.2x slower</td>
<td>Identical</td>
</tr>
<tr>
<th>traceur</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>1.3x slower</td>
<td>Identical</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td></td>
<td></td>
<td>Identical</td>
</tr>
</tbody>
</table>
<p>Issues:</p>
<ul>
<li><a href="https://bugzilla.mozilla.org/show_bug.cgi?id=1177319">Firefox</a></li>
<li><a href="https://github.com/babel/babel/pull/1821">Babel Strict Optimization</a></li>
</ul>
<h3>
<a id="user-content-default-parameters" class="anchor" href="#default-parameters" aria-hidden="true"><span class="octicon octicon-link"></span></a>Default Parameters</h3>
<p>Default parameters were universally slower for all transpiler implementations. They effectively compile to the same thing, utilizing the <code>arguments</code> object to set a local variable vs. using a named paramemter. This appears to be unoptimzed under all engines and consequently performance was 4-2000x slower. Sadly, this is required in order to properly implement the <a href="https://github.com/babel/babel/issues/1814"><code>fn.length</code></a> behavior defined by the spec:</p>
<blockquote cite="http://www.ecma-international.org/ecma-262/6.0/#sec-function-definitions-static-semantics-expectedargumentcount"><p>
NOTE The ExpectedArgumentCount of a FormalParameterList is the number of FormalParameters to the left of either the rest parameter or the first FormalParameter with an Initializer. A FormalParameter without an initializer is allowed after the first parameter with an initializer but such parameters are considered to be optional with undefined as their default value.
</p></blockquote>
<p>These scaled numbers should be taken in context. The ES5 equivalents are highly optimized, Firefox pushing over 833 million operations a second in one test, so the net performance of the transpiled versions may very well be sufficient for most use cases, particularly those not on the hot path.</p>
<p>The only native implementation, Firefox, performed identically to the ES5 implementation.</p>
<table>
<thead>
<tr>
<td colspan="2"></td>
<th colspan="2">node</th>
<th colspan="3">chrome</th>
<th colspan="3">firefox</th>
<th colspan="2">internet explorer</th>
<th colspan="1">safari</th>
</tr>
<tr>
<td colspan="2"></td>
<th>0.10.39</th>
<th>2.3.0</th>
<th>43</th>
<th>44</th>
<th>45</th>
<th>38</th>
<th>39</th>
<th>40</th>
<th>11</th>
<th>12</th>
<th>8</th>
</tr>
</thead>
<tbody>
<tr id="user-content-test-classes">
<th rowspan="4">
          classes<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/classes">tests</a>
        </th>
<th>babel</th>
<td>2.3x slower</td>
<td>1.5x slower</td>
<td>1.3x slower</td>
<td>1.4x slower</td>
<td>1.3x slower</td>
<td>27x slower</td>
<td>26x slower</td>
<td>30x slower</td>
<td>1.4x slower</td>
<td>1.5x slower</td>
<td>Identical</td>
</tr>
<tr>
<th>babel-loose</th>
<td>2.3x slower</td>
<td>1.5x slower</td>
<td>1.3x slower</td>
<td>1.4x slower</td>
<td>1.3x slower</td>
<td>6x slower</td>
<td>6x slower</td>
<td>8x slower</td>
<td>1.3x slower</td>
<td>2.4x slower</td>
<td>Identical</td>
</tr>
<tr>
<th>traceur</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>1.5x slower</td>
<td>1.7x slower</td>
<td>1.8x slower</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr id="user-content-test-defaults">
<th rowspan="3">
          defaults<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/defaults">tests</a>
        </th>
<th>babel</th>
<td>17x slower</td>
<td>11x slower</td>
<td>11x slower</td>
<td>9x slower</td>
<td>8x slower</td>
<td>1842x slower</td>
<td>2051x slower</td>
<td>2043x slower</td>
<td>229x slower</td>
<td>72x slower</td>
<td>4x slower</td>
</tr>
<tr>
<th>traceur</th>
<td>16x slower</td>
<td>12x slower</td>
<td>12x slower</td>
<td>10x slower</td>
<td>9x slower</td>
<td>1759x slower</td>
<td>2305x slower</td>
<td>1974x slower</td>
<td>210x slower</td>
<td>73x slower</td>
<td>4x slower</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table>
<h3>
<a id="user-content-rest-parameters" class="anchor" href="#rest-parameters" aria-hidden="true"><span class="octicon octicon-link"></span></a>Rest Parameters</h3>
<p>Rest parameters are as fast or faster than the ES5 equivalent under almost all implementations. Native implementations provided a performance boost up to 40x (with the exception of V8 where it causes a <a href="https://code.google.com/p/v8/issues/detail?id=2159#c18">known deoptimization</a>). Use them with a transpiler. They're great. Death to <code>arguments</code>.</p>
<table>
<thead>
<tr>
<td colspan="2"></td>
<th colspan="2">node</th>
<th colspan="3">chrome</th>
<th colspan="3">firefox</th>
<th colspan="2">internet explorer</th>
<th colspan="1">safari</th>
</tr>
<tr>
<td colspan="2"></td>
<th>0.10.39</th>
<th>2.3.0</th>
<th>43</th>
<th>44</th>
<th>45</th>
<th>38</th>
<th>39</th>
<th>40</th>
<th>11</th>
<th>12</th>
<th>8</th>
</tr>
</thead>
<tbody>
<tr id="user-content-test-rest">
<th rowspan="3">
          rest<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/rest">tests</a>
        </th>
<th>babel</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>2.0x slower</td>
<td>1.5x slower</td>
<td>Identical</td>
</tr>
<tr>
<th>traceur</th>
<td>Identical</td>
<td>1.5x faster</td>
<td>1.2x faster</td>
<td>1.2x faster</td>
<td>1.2x faster</td>
<td>31x faster</td>
<td>34x faster</td>
<td>27x faster</td>
<td>2.4x slower</td>
<td>1.6x slower</td>
<td>1.3x faster</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td>3x slower</td>
<td></td>
<td></td>
<td></td>
<td>40x faster</td>
<td>42x faster</td>
<td>32x faster</td>
<td></td>
<td>8x faster</td>
<td></td>
</tr>
</tbody>
</table>
<h3>
<a id="user-content-spread-parameters" class="anchor" href="#spread-parameters" aria-hidden="true"><span class="octicon octicon-link"></span></a>Spread Parameters</h3>
<p>Under Babel, spread parameters for arrays perform identically to the ES5 counterpart as they are effectively both an <code>apply</code> call. Under Traceur and all of the native implementations the implementations are 1.3x to 17x slower.</p>
<table>
<thead>
<tr>
<td colspan="2"></td>
<th colspan="2">node</th>
<th colspan="3">chrome</th>
<th colspan="3">firefox</th>
<th colspan="2">internet explorer</th>
<th colspan="1">safari</th>
</tr>
<tr>
<td colspan="2"></td>
<th>0.10.39</th>
<th>2.3.0</th>
<th>43</th>
<th>44</th>
<th>45</th>
<th>38</th>
<th>39</th>
<th>40</th>
<th>11</th>
<th>12</th>
<th>8</th>
</tr>
</thead>
<tbody>
<tr id="user-content-test-spread">
<th rowspan="3">
          spread<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/spread">tests</a>
        </th>
<th>babel</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
</tr>
<tr>
<th>traceur</th>
<td>13x slower</td>
<td>6x slower</td>
<td>5x slower</td>
<td>6x slower</td>
<td>6x slower</td>
<td>2.7x slower</td>
<td>3x slower</td>
<td>3x slower</td>
<td>9x slower</td>
<td>712x slower</td>
<td>17x slower</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td>3x slower</td>
<td>4x slower</td>
<td>4x slower</td>
<td></td>
<td>1.3x slower</td>
<td>2.5x slower</td>
</tr>
<tr id="user-content-test-spread-generator">
<th rowspan="4">
          spread-generator<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/spread-generator">tests</a>
        </th>
<th>babel</th>
<td>150x slower</td>
<td>55x slower</td>
<td>58x slower</td>
<td>66x slower</td>
<td>71x slower</td>
<td>153x slower</td>
<td>143x slower</td>
<td>157x slower</td>
<td>656x slower</td>
<td>423x slower</td>
<td>64x slower</td>
</tr>
<tr>
<th>babel-loose</th>
<td>114x slower</td>
<td>46x slower</td>
<td>41x slower</td>
<td>45x slower</td>
<td>53x slower</td>
<td>60x slower</td>
<td>60x slower</td>
<td>61x slower</td>
<td>28x slower</td>
<td>51x slower</td>
<td>22x slower</td>
</tr>
<tr>
<th>traceur</th>
<td>27x slower</td>
<td>10x slower</td>
<td>9x slower</td>
<td>10x slower</td>
<td>13x slower</td>
<td>11x slower</td>
<td>10x slower</td>
<td>11x slower</td>
<td>15x slower</td>
<td>1551x slower</td>
<td>9x slower</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td>7x slower</td>
<td>8x slower</td>
<td>7x slower</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr id="user-content-test-spread-literal">
<th rowspan="3">
          spread-literal<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/spread-literal">tests</a>
        </th>
<th>babel</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>1.2x slower</td>
<td>Identical</td>
<td>1.2x slower</td>
<td>3x slower</td>
<td>1.7x slower</td>
<td>2.0x slower</td>
</tr>
<tr>
<th>traceur</th>
<td>6x slower</td>
<td>3.0x slower</td>
<td>2.1x slower</td>
<td>2.5x slower</td>
<td>2.3x slower</td>
<td>6x slower</td>
<td>5x slower</td>
<td>5x slower</td>
<td>26x slower</td>
<td>451x slower</td>
<td>9x slower</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td>8x slower</td>
<td>7x slower</td>
<td>8x slower</td>
<td></td>
<td>1.8x slower</td>
<td>3x slower</td>
</tr>
</tbody>
</table>
<h3>
<a id="user-content-let--const" class="anchor" href="#let--const" aria-hidden="true"><span class="octicon octicon-link"></span></a>Let + Const</h3>
<p><code>let</code> and <code>const</code> bindings were pretty much identical across the board. While these don't offer performance improvements (yet), they shouldn't negatively impact performance.</p>
<table>
<thead>
<tr>
<td colspan="2"></td>
<th colspan="2">node</th>
<th colspan="3">chrome</th>
<th colspan="3">firefox</th>
<th colspan="2">internet explorer</th>
<th colspan="1">safari</th>
</tr>
<tr>
<td colspan="2"></td>
<th>0.10.39</th>
<th>2.3.0</th>
<th>43</th>
<th>44</th>
<th>45</th>
<th>38</th>
<th>39</th>
<th>40</th>
<th>11</th>
<th>12</th>
<th>8</th>
</tr>
</thead>
<tbody>
<tr id="user-content-test-bindings">
<th rowspan="3">
          bindings<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/bindings">tests</a>
        </th>
<th>babel</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
</tr>
<tr>
<th>traceur</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>1.3x faster</td>
<td>Identical</td>
</tr>
<tr>
<th>es6</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>2.3x slower</td>
<td>Identical</td>
<td>Identical</td>
<td>1.3x slower</td>
<td>Identical</td>
<td></td>
</tr>
</tbody>
</table>
<h3>
<a id="user-content-forof" class="anchor" href="#forof" aria-hidden="true"><span class="octicon octicon-link"></span></a>For..of</h3>
<p><code>for..of</code> is universally slower, ranging from 3 to 20x slower for array iteration over classical array iteration. When iterating over an object with a custom iterator, the performance is also much slower than <code>for..in</code> iteration with <code>hasOwnProperty</code> checks. </p>
<table>
<thead>
<tr>
<td colspan="2"></td>
<th colspan="2">node</th>
<th colspan="3">chrome</th>
<th colspan="3">firefox</th>
<th colspan="2">internet explorer</th>
<th colspan="1">safari</th>
</tr>
<tr>
<td colspan="2"></td>
<th>0.10.39</th>
<th>2.3.0</th>
<th>43</th>
<th>44</th>
<th>45</th>
<th>38</th>
<th>39</th>
<th>40</th>
<th>11</th>
<th>12</th>
<th>8</th>
</tr>
</thead>
<tbody>
<tr id="user-content-test-for-of-array">
<th rowspan="4">
          for-of-array<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/for-of-array">tests</a>
        </th>
<th>babel</th>
<td>21x slower</td>
<td>8x slower</td>
<td>8x slower</td>
<td>9x slower</td>
<td>9x slower</td>
<td>14x slower</td>
<td>20x slower</td>
<td>19x slower</td>
<td>15x slower</td>
<td>489x slower</td>
<td>7x slower</td>
</tr>
<tr>
<th>babel-loose</th>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>Identical</td>
<td>1.4x slower</td>
<td>1.7x slower</td>
<td>1.7x slower</td>
<td>1.3x slower</td>
<td>Identical</td>
<td>1.8x slower</td>
</tr>
<tr>
<th>traceur</th>
<td>12x slower</td>
<td>7x slower</td>
<td>7x slower</td>
<td>8x slower</td>
<td>8x slower</td>
<td>26x slower</td>
<td>31x slower</td>
<td>32x slower</td>
<td>9x slower</td>
<td>6x slower</td>
<td>6x slower</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td>6x slower</td>
<td>6x slower</td>
<td>8x slower</td>
<td>8x slower</td>
<td>5x slower</td>
<td>7x slower</td>
<td>7x slower</td>
<td></td>
<td>2.9x slower</td>
<td>4x slower</td>
</tr>
<tr id="user-content-test-for-of-object">
<th rowspan="4">
          for-of-object<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/for-of-object">tests</a>
        </th>
<th>babel</th>
<td>10x slower</td>
<td>7x slower</td>
<td>8x slower</td>
<td>9x slower</td>
<td>6x slower</td>
<td>6x slower</td>
<td>6x slower</td>
<td>5x slower</td>
<td>60x slower</td>
<td>11x slower</td>
<td>6x slower</td>
</tr>
<tr>
<th>babel-loose</th>
<td>10x slower</td>
<td>6x slower</td>
<td>7x slower</td>
<td>9x slower</td>
<td>7x slower</td>
<td>4x slower</td>
<td>4x slower</td>
<td>4x slower</td>
<td>6x slower</td>
<td>449x slower</td>
<td>6x slower</td>
</tr>
<tr>
<th>traceur</th>
<td>8x slower</td>
<td>7x slower</td>
<td>8x slower</td>
<td>9x slower</td>
<td>7x slower</td>
<td>9x slower</td>
<td>10x slower</td>
<td>9x slower</td>
<td>5x slower</td>
<td>4x slower</td>
<td>4x slower</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td>6x slower</td>
<td>7x slower</td>
<td>8x slower</td>
<td>6x slower</td>
<td>3x slower</td>
<td>3x slower</td>
<td>3x slower</td>
<td></td>
<td>3x slower</td>
<td></td>
</tr>
</tbody>
</table>
<h3>
<a id="user-content-generators" class="anchor" href="#generators" aria-hidden="true"><span class="octicon octicon-link"></span></a>Generators</h3>
<p>Much like <code>for..of</code>, generators are also quite a bit slower than a raw ES5 implementation of the iterable protocol, with performance ranging from 10x to 750x slower. There is hope here as the V8 implementation achieves parity with the ES5 implementation.</p>
<table>
<thead>
<tr>
<td colspan="2"></td>
<th colspan="2">node</th>
<th colspan="3">chrome</th>
<th colspan="3">firefox</th>
<th colspan="2">internet explorer</th>
<th colspan="1">safari</th>
</tr>
<tr>
<td colspan="2"></td>
<th>0.10.39</th>
<th>2.3.0</th>
<th>43</th>
<th>44</th>
<th>45</th>
<th>38</th>
<th>39</th>
<th>40</th>
<th>11</th>
<th>12</th>
<th>8</th>
</tr>
</thead>
<tbody>
<tr id="user-content-test-generator">
<th rowspan="3">
          generator<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/generator">tests</a>
        </th>
<th>babel</th>
<td>601x slower</td>
<td>65x slower</td>
<td>67x slower</td>
<td>63x slower</td>
<td>69x slower</td>
<td>754x slower</td>
<td>723x slower</td>
<td>716x slower</td>
<td>79x slower</td>
<td>499x slower</td>
<td>78x slower</td>
</tr>
<tr>
<th>traceur</th>
<td>77x slower</td>
<td>10x slower</td>
<td>10x slower</td>
<td>10x slower</td>
<td>12x slower</td>
<td>48x slower</td>
<td>50x slower</td>
<td>46x slower</td>
<td>18x slower</td>
<td>112x slower</td>
<td>10x slower</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td>Identical</td>
<td>1.2x slower</td>
<td>Identical</td>
<td>Identical</td>
<td>22x slower</td>
<td>20x slower</td>
<td>19x slower</td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table>
<p>Issues:</p>
<ul>
<li><a href="https://github.com/facebook/regenerator/pull/207">Regenerator Init Optimization</a></li>
<li><a href="https://github.com/facebook/regenerator/pull/208">Regenerator Exec Optimization</a></li>
</ul>
<h3>
<a id="user-content-maps-and-sets" class="anchor" href="#maps-and-sets" aria-hidden="true"><span class="octicon octicon-link"></span></a>Maps and Sets</h3>
<p><code>Map</code> and <code>Set</code> all have insert performance that is about 10x slower for a moderately sized data set. All of the implementations show massive improvement on the lookup operations, with Firefox's native implementation showing a 200x speed increase for a dataset of size 500.</p>
<p>Traceur appears to delegate to the native implementation via their polyfill where possible so performance is closely linked to improvements in the native layer. In runtime mode, Babel does not appear to delegate and performance suffers as a result. Babel's polyfill mode should behave as Traceur does but this was not directly tested.</p>
<p>Take caution with these numbers. The tests use a data set of size 500 and other data sets will have varying performance but it appears that these features are ready for general use if you have many reads and few writes or need to have objects as keys.</p>
<table>
<thead>
<tr>
<td colspan="2"></td>
<th colspan="2">node</th>
<th colspan="3">chrome</th>
<th colspan="3">firefox</th>
<th colspan="2">internet explorer</th>
<th colspan="1">safari</th>
</tr>
<tr>
<td colspan="2"></td>
<th>0.10.39</th>
<th>2.3.0</th>
<th>43</th>
<th>44</th>
<th>45</th>
<th>38</th>
<th>39</th>
<th>40</th>
<th>11</th>
<th>12</th>
<th>8</th>
</tr>
</thead>
<tbody>
<tr id="user-content-test-map-set">
<th rowspan="3">
          map-set<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/map-set">tests</a>
        </th>
<th>babel</th>
<td>13x slower</td>
<td>3.0x slower</td>
<td>5x slower</td>
<td>5x slower</td>
<td>3.0x slower</td>
<td>105x slower</td>
<td>12x slower</td>
<td>15x slower</td>
<td>60x slower</td>
<td>19x slower</td>
<td>79x slower</td>
</tr>
<tr>
<th>traceur</th>
<td>5x slower</td>
<td>Identical</td>
<td>2.2x slower</td>
<td>2.3x slower</td>
<td>1.6x slower</td>
<td>20x slower</td>
<td>2.5x slower</td>
<td>2.4x slower</td>
<td>28x slower</td>
<td>2.2x slower</td>
<td>16x slower</td>
</tr>
<tr>
<th>es6</th>
<td>5x slower</td>
<td>Identical</td>
<td>2.2x slower</td>
<td>2.3x slower</td>
<td>1.6x slower</td>
<td>21x slower</td>
<td>2.2x slower</td>
<td>2.7x slower</td>
<td>28x slower</td>
<td>2.2x slower</td>
<td>15x slower</td>
</tr>
</tbody>
</table>
<h3>
<a id="user-content-promises" class="anchor" href="#promises" aria-hidden="true"><span class="octicon octicon-link"></span></a>Promises</h3>
<p>Promises are across the board faster with both the polyfill and native implementations. This particular benchmark is dubious as it's both async and inheriently tied to long running behaviors where execution overhead has little impact.</p>
<table>
<thead>
<tr>
<td colspan="2"></td>
<th colspan="2">node</th>
<th colspan="3">chrome</th>
<th colspan="3">firefox</th>
<th colspan="2">internet explorer</th>
<th colspan="1">safari</th>
</tr>
<tr>
<td colspan="2"></td>
<th>0.10.39</th>
<th>2.3.0</th>
<th>43</th>
<th>44</th>
<th>45</th>
<th>38</th>
<th>39</th>
<th>40</th>
<th>11</th>
<th>12</th>
<th>8</th>
</tr>
</thead>
<tbody>
<tr id="user-content-test-promises">
<th rowspan="3">
          promises<br />
          <a href="https://github.com/kpdecker/six-speed/tree/master/tests/promises">tests</a>
        </th>
<th>babel</th>
<td>Identical</td>
<td>Identical</td>
<td>6x faster</td>
<td>7x faster</td>
<td>7x faster</td>
<td>21x faster</td>
<td>17x faster</td>
<td>16x faster</td>
<td>Identical</td>
<td>1.2x slower</td>
<td>3x faster</td>
</tr>
<tr>
<th>traceur</th>
<td>Identical</td>
<td>Identical</td>
<td>2.0x faster</td>
<td>2.0x faster</td>
<td>1.9x faster</td>
<td>36x faster</td>
<td>31x faster</td>
<td>Identical</td>
<td>Identical</td>
<td>1.8x slower</td>
<td>3x faster</td>
</tr>
<tr>
<th>es6</th>
<td></td>
<td>Identical</td>
<td>2.0x faster</td>
<td>2.0x faster</td>
<td>1.9x faster</td>
<td>37x faster</td>
<td>29x faster</td>
<td>Identical</td>
<td></td>
<td>1.9x slower</td>
<td>3x faster</td>
</tr>
</tbody>
</table>

## Testing methodology
<p>For each of the ES6 features in question, a ES5 implementation of that functionality was written along with a ES6 version. It should be noted that the functionality is frequently the same, but in some cases the "common" vs. "correct" version was written, i.e. using <code>x[key] = value</code> vs. <code>defineProperty</code> which is faster but can be hit but a particular nasty edge case for those who deem it fun to extend <code>Object.prototype</code>.</p>
<p>Babel, in both loose+runtime and runtime mode, and Traceur were then used to compile the ES6 version to a ES5 compliant version, utilizing the runtime over polyfill to maintain test isolation and avoid native implementations where possible.</p>
<p>All of these test instances were then benchmarked in the given JavaScript engine using <a href="http://benchmarkjs.com/">Benchmark.js</a> and then the operations per second compared to the ES5 implementation. Cross browser and cross execution comparisions are avoided as much as possible to isolate environmental issues when executing on VMs in the cloud.</p>
<p>All of this data, including any updates from more recent test runs is available at <a href="http://kpdecker.github.io/six-speed/">http://kpdecker.github.io/six-speed/</a> and the test suite is available at <a href="https://github.com/kpdecker/six-speed">https://github.com/kpdecker/six-speed</a> for review/feedback.</p>
<h2>
<a id="user-content-takeaways" class="anchor" href="#takeaways" aria-hidden="true"><span class="octicon octicon-link"></span></a>Takeaways</h2>
<p>As noted above, these results might not be representative of your own application since they only test very small subsets of inputs and behaviors of these new features. If you are finding that you have performance issues with your code using these features, you should test them within your own environment to see what the actual behavior is.</p>
<p>While some of these features are a bit slow as of this writing, their performance should only improve as the native implementations mature and are optimized as real world use is applied to them. For the transpilers, there are some performance optimzations that can be made but much of the overhead they are experiencing is due to spec compliance, which they go to great lengths to achieve, but this comes with unfortunate overhead under ES5 implementations as they stand. Babel's loose mode does offer a bit of a performance boost, but care must be taken when using loose mode as this could cause breakages when code is migrated to the standard native implementations.</p>
<p>Personally I intend to start using most of these features where they make sense but will avoid designing core APIs around these features (perhaps with the exception of Promises) until the native implementations have matured a bit.</p>
