## What is a Category?

A category $C$ consists of

- a collection $Obj$ of entities called **objects**

- a collection $Arw$ of entities called **arrows**

- three assignments:

  - source: $Arw \to Obj$
  - target: $Arw \to Obj$
  - id: $Obj \to Arw$

- a partial composition: $Arw \times Arw \to Arw$
  

### Required Properties

- If we have to arrows:
  $$
  f: A \to B, g: B \to C
  $$
  Then another arrow
  $$
  g\circ f: A \to C
  $$
  is formed.

- The composition of arrows are associative, that is:
  $$
  (h \circ g) \circ f = h \circ (g \circ g)
  $$

### Notions

In a category $C$, given two arbitrary object $A, B$, the arrow class of the arrows between them is denoted by:
$$
C[A, B], ~ C(A, B),~ \textbf{Hom}_{C}(A, B)
$$
It is called the **hom-set**. When there is no ambiguity, we may also write $[A, B]$ for $C[A, B]$.

Consider a triangle of arrows:


<div class="svg-scroll">
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0" y="0" width="702" height="149" style="
        width:702px;
        height:149px;
        background: #FFF;
        fill: none;
">
<svg xmlns="http://www.w3.org/2000/svg"/>
<svg xmlns="http://www.w3.org/2000/svg" class="role-diagram-draw-area" style="overflow: visible;"><g class="shapes-region" style="stroke: black; fill: none;"><g class="grouped-shape"><g class="composite-shape"><path class="real" d=" M335.5,9.25 C335.5,6.35 337.85,4 340.75,4 C343.65,4 346,6.35 346,9.25 C346,12.15 343.65,14.5 340.75,14.5 C337.85,14.5 335.5,12.15 335.5,9.25 Z" style="stroke-width: 1px; stroke: rgb(0, 0, 0); fill: rgb(0, 0, 0); fill-opacity: 1;"/></g><g class="composite-shape"><path class="real" d=" M245.5,131.25 C245.5,128.35 247.85,126 250.75,126 C253.65,126 256,128.35 256,131.25 C256,134.15 253.65,136.5 250.75,136.5 C247.85,136.5 245.5,134.15 245.5,131.25 Z" style="stroke-width: 1px; stroke: rgb(0, 0, 0); fill: rgb(0, 0, 0); fill-opacity: 1;"/></g><g class="composite-shape"><path class="real" d=" M439.5,127.25 C439.5,124.35 441.85,122 444.75,122 C447.65,122 450,124.35 450,127.25 C450,130.15 447.65,132.5 444.75,132.5 C441.85,132.5 439.5,130.15 439.5,127.25 Z" style="stroke-width: 1px; stroke: rgb(0, 0, 0); fill: rgb(0, 0, 0); fill-opacity: 1;"/></g><g class="arrow-line"><path class="connection real" stroke-dasharray="" d="  M265,131.5 L428,129.52" style="stroke: rgb(0, 0, 0); stroke-width: 1px; fill: none; fill-opacity: 1;"/><g stroke="#000" transform="matrix(-0.9999274866995999,0.012042480750311954,-0.012042480750311954,-0.9999274866995999,430,129.5)" style="stroke: rgb(0, 0, 0); stroke-width: 1px;"><path d=" M10.93,-3.29 Q4.96,-0.45 0,0 Q4.96,0.45 10.93,3.29"/></g></g><g class="arrow-line"><path class="connection real" stroke-dasharray="" d="  M256,118.5 L329.79,21.09" style="stroke: rgb(0, 0, 0); stroke-width: 1px; fill: none; fill-opacity: 1;"/><g stroke="#000" transform="matrix(-0.6039037812533281,0.7970572269215884,-0.7970572269215884,-0.6039037812533281,331,19.5)" style="stroke: rgb(0, 0, 0); stroke-width: 1px;"><path d=" M10.93,-3.29 Q4.96,-0.45 0,0 Q4.96,0.45 10.93,3.29"/></g></g><g class="arrow-line"><path class="connection real" stroke-dasharray="" d="  M351,18 L432.73,116.96" style="stroke: rgb(0, 0, 0); stroke-width: 1px; fill: none; fill-opacity: 1;"/><g stroke="#000" transform="matrix(-0.6367513474701519,-0.7710692066831263,0.7710692066831263,-0.6367513474701519,434,118.5)" style="stroke: rgb(0, 0, 0); stroke-width: 1px;"><path d=" M10.93,-3.29 Q4.96,-0.45 0,0 Q4.96,0.45 10.93,3.29"/></g></g></g><g/></g><g/><g/><g/></svg>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="700" height="147" style="width:700px;height:147px;font-family:Asana-Math, Asana;background:#FFF;"><g><g><g><text x="275" y="59.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">f</text></g></g></g><g><g><g><text x="401" y="54.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">g</text></g></g></g><g><g><g><text x="336.01666259765625" y="105.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">h</text></g></g></g></svg>
</svg>
</div>

$g \circ f$ and $h$ gives a **parallel pair** of arrows between two objects.

If $h = g \circ f$, we say the triangle **commutes.**

## Categories of Structured Set

### Monoid Category

A monoid is a structured set discribed by
$$
(R, \star, 1)
$$
where the $\star$ is a binary operation on the set $R$ with identity $1$.

A homomorphism between two monoids $A$ and $B$ is a functions such that:
$$
\phi(r \star_A s) = \phi(r) \star_B \phi(s), \phi(1_A) = 1_B
$$
Monoids form a category and the arrows are the homomorphisms. This category is denoted by $\mathbf{Mon}$.

### Ordered Set

A pre-order (quasi-order) relation $\le$ is a reflexive and transitive binary relation.

A partial order relation is a anti-symmetric pre-order relation.

We call a set $S$ furbished with a pre-order (partial-order) relation as a preset (poset).

A **monotone map** is a function between two presets such that:
$$
x \le y \Rightarrow f(x) \le f(y)
$$
Hence, presets (posets) with monotone maps as arrows form a category called $\mathbf{Pre}$ ($\mathbf{Pos}$).

One particular thing to notice is that given two posets $R, S$ (each of which are also a preset), the following arrow collections are actually the same:
$$
\textbf{Pre}[R, S] ~~~~ \textbf{Pos}[R,S]
$$
which means that $\textbf{Pos}$ is a full-subcategory of $\textbf{Pre}$

### Set with Partial Functions

It is also possible to define a category of sets with partial functions as arrows.

If there is an arrow of partial function $f$ between two set $A, B$, then there must be a subset $X$ of $A$, where the arrow works as a total function $\overline f$ between $X$ and $B$ by limiting the domain to $X$.



<div class="svg-scroll">
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0" y="0" width="662" height="86" style="
        width:662px;
        height:86px;
        background: #FFF;
        fill: none;
">
<svg xmlns="http://www.w3.org/2000/svg" class="role-diagram-draw-area"><g class="shapes-region" style="stroke: black; fill: none;"><g class="grouped-shape"/><g/></g><g><g class="connection-group"><g class="arrow-line"><path class="connection real" stroke-dasharray="" d="  M285,58 L285,32" style="stroke: rgb(0, 0, 0); stroke-width: 1px; fill: none; fill-opacity: 1;"/><g stroke="#000" transform="matrix(3.061616997868383e-16,1,-1,3.061616997868383e-16,285,30)" style="stroke: rgb(0, 0, 0); stroke-width: 1px;"><path d=" M10.93,-3.29 Q4.96,-0.45 0,0 Q4.96,0.45 10.93,3.29"/></g></g></g><g class="connection-group"><g class="arrow-line"><path class="connection real" stroke-dasharray="" d="  M294,65.33 L368.27,22.67" style="stroke: rgb(0, 0, 0); stroke-width: 1px; fill: none; fill-opacity: 1;"/><g stroke="#000" transform="matrix(-0.8670707011644901,0.49818510533949073,-0.49818510533949073,-0.8670707011644901,370.00000000000006,21.670212765957444)" style="stroke: rgb(0, 0, 0); stroke-width: 1px;"><path d=" M10.93,-3.29 Q4.96,-0.45 0,0 Q4.96,0.45 10.93,3.29"/></g></g></g></g><g/><g/></svg>
        <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="660" height="84" style="width:660px;height:84px;font-family:Asana-Math, Asana;background:#FFF;"><g><g><g><g><g><g><g transform="matrix(1,0,0,1,277.0999755859375,53.9666748046875)"><path transform="matrix(0.017,0,0,-0.017,0,0)" d="M615 8L615 61L309 61C207 61 107 153 107 271C107 382 195 480 309 480L615 480L615 533L314 533C167 533 55 411 55 271C55 131 167 8 314 8Z" stroke="rgb(144,19,254)" stroke-opacity="1" stroke-width="8" fill="rgb(144,19,254)" fill-opacity="1"></path></g></g></g></g></g></g></g><g><g><g><text x="280" y="61.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">X</text></g></g></g><g><g><g><text x="374.01666259765625" y="7.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">B</text></g></g></g><g><g><g><text x="280" y="8.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">A</text></g></g></g></svg>
</svg>
</div>



The composition, however, is a little bit complicated:



<div class="svg-scroll">
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0" y="0" width="662" height="176" style="
        width:662px;
        height:176px;
        background: #FFF;
        fill: none;
">
<svg xmlns="http://www.w3.org/2000/svg"><g><defs><pattern id=".8088418854340441" width="10" height="10" patternUnits="userSpaceOnUse"><path d="M 10 0 L 0 0 0 10" fill="none" stroke="lightgray" stroke-width="0.5"/></pattern></defs><rect width="100%" height="100%" fill="url(#.8088418854340441)" stroke="lightgray" stroke-width="0.5"/></g></svg>
<svg xmlns="http://www.w3.org/2000/svg" class="role-diagram-draw-area"><g class="shapes-region" style="stroke: black; fill: none;"><g/></g><g><g class="connection-group"><g class="arrow-line"><path class="connection real" stroke-dasharray="" d="  M254,93.05 L333.37,36.12" style="stroke: rgb(0, 0, 0); stroke-width: 1px; fill: none; fill-opacity: 1;"/><g stroke="#000" transform="matrix(-0.812592453381668,0.5828323126827833,-0.5828323126827833,-0.812592453381668,334.99999999999994,34.95454545454544)" style="stroke: rgb(0, 0, 0); stroke-width: 1px;"><path d=" M10.93,-3.29 Q4.96,-0.45 0,0 Q4.96,0.45 10.93,3.29"/></g></g></g><g class="connection-group"><g class="arrow-line"><path class="connection real" stroke-dasharray="" d="  M245,87 L245,45" style="stroke: rgb(0, 0, 0); stroke-width: 1px; fill: none; fill-opacity: 1;"/><g stroke="#000" transform="matrix(3.061616997868383e-16,1,-1,3.061616997868383e-16,245.00000000000003,43)" style="stroke: rgb(0, 0, 0); stroke-width: 1px;"><path d=" M10.93,-3.29 Q4.96,-0.45 0,0 Q4.96,0.45 10.93,3.29"/></g></g></g><g class="connection-group"><g class="arrow-line"><path class="connection real" stroke-dasharray="" d="  M254,148.48 L332.23,107.18" style="stroke: rgb(0, 0, 0); stroke-width: 1px; fill: none; fill-opacity: 1;"/><g stroke="#000" transform="matrix(-0.8843366544959593,0.46684974190299633,-0.46684974190299633,-0.8843366544959593,334.00000000000006,106.251269035533)" style="stroke: rgb(0, 0, 0); stroke-width: 1px;"><path d=" M10.93,-3.29 Q4.96,-0.45 0,0 Q4.96,0.45 10.93,3.29"/></g></g></g><g class="connection-group"><g class="arrow-line"><path class="connection real" stroke-dasharray="" d="  M343.17,89 L343.8,43" style="stroke: rgb(0, 0, 0); stroke-width: 1px; fill: none; fill-opacity: 1;"/><g stroke="#000" transform="matrix(-0.013613147670749143,0.9999073368120139,-0.9999073368120139,-0.013613147670749143,343.82876712328766,41)" style="stroke: rgb(0, 0, 0); stroke-width: 1px;"><path d=" M10.93,-3.29 Q4.96,-0.45 0,0 Q4.96,0.45 10.93,3.29"/></g></g></g><g class="connection-group"><g class="arrow-line"><path class="connection real" stroke-dasharray="" d="  M244.62,141 L244.87,114" style="stroke: rgb(0, 0, 0); stroke-width: 1px; fill: none; fill-opacity: 1;"/><g stroke="#000" transform="matrix(-0.00925011311679609,0.9999572167884616,-0.9999572167884616,-0.00925011311679609,244.88425925925924,112)" style="stroke: rgb(0, 0, 0); stroke-width: 1px;"><path d=" M10.93,-3.29 Q4.96,-0.45 0,0 Q4.96,0.45 10.93,3.29"/></g></g></g><g class="connection-group"><g class="arrow-line"><path class="connection real" stroke-dasharray="" d="  M352,93.92 L417.47,38.79" style="stroke: rgb(0, 0, 0); stroke-width: 1px; fill: none; fill-opacity: 1;"/><g stroke="#000" transform="matrix(-0.764921400918431,0.6441236297613875,-0.6441236297613875,-0.764921400918431,419.00000000000006,37.5)" style="stroke: rgb(0, 0, 0); stroke-width: 1px;"><path d=" M10.93,-3.29 Q4.96,-0.45 0,0 Q4.96,0.45 10.93,3.29"/></g></g></g><g class="connection-group"><g class="arrow-line"><path class="connection real" stroke-dasharray="" d="  M254,30.32 L333,28.72" style="stroke: rgb(0, 0, 0); stroke-width: 1px; fill: none; fill-opacity: 1;"/><g stroke="#000" transform="matrix(-0.99979506040039,0.020244436247536024,-0.020244436247536024,-0.99979506040039,335.00000000000006,28.68181818181818)" style="stroke: rgb(0, 0, 0); stroke-width: 1px;"><path d=" M10.93,-3.29 Q4.96,-0.45 0,0 Q4.96,0.45 10.93,3.29"/></g></g></g><g class="connection-group"><g class="arrow-line"><path class="connection real" stroke-dasharray="" d="  M353,28.61 L417,29.36" style="stroke: rgb(0, 0, 0); stroke-width: 1px; fill: none; fill-opacity: 1;"/><g stroke="#000" transform="matrix(-0.9999295732792147,-0.011867960298537263,0.011867960298537263,-0.9999295732792147,419,29.387573964497037)" style="stroke: rgb(0, 0, 0); stroke-width: 1px;"><path d=" M10.93,-3.29 Q4.96,-0.45 0,0 Q4.96,0.45 10.93,3.29"/></g></g></g></g><g/><g/></svg>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="660" height="174" style="width:660px;height:174px;font-family:Asana-Math, Asana;background:#FFF;"><g><g><g><g><g><g><g transform="matrix(1,0,0,1,233.0999755859375,75.96665954589844)"><path transform="matrix(0.017,0,0,-0.017,0,0)" d="M615 8L615 61L309 61C207 61 107 153 107 271C107 382 195 480 309 480L615 480L615 533L314 533C167 533 55 411 55 271C55 131 167 8 314 8Z" stroke="rgb(144,19,254)" stroke-opacity="1" stroke-width="8" fill="rgb(144,19,254)" fill-opacity="1"></path></g></g></g></g></g></g></g><g><g><g><text x="240" y="90.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">X</text></g></g></g><g><g><g><text x="339.01666259765625" y="19.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">B</text></g></g></g><g><g><g><text x="240" y="21.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">A</text></g></g></g><g><g><g><g><g><g><g transform="matrix(1,0,0,1,335.0999755859375,68.96665954589844)"><path transform="matrix(0.017,0,0,-0.017,0,0)" d="M615 8L615 61L309 61C207 61 107 153 107 271C107 382 195 480 309 480L615 480L615 533L314 533C167 533 55 411 55 271C55 131 167 8 314 8Z" stroke="rgb(144,19,254)" stroke-opacity="1" stroke-width="8" fill="rgb(144,19,254)" fill-opacity="1"></path></g></g></g></g></g></g></g><g><g><g><text x="423" y="20.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">C</text></g></g></g><g><g><g><g><g><g><g transform="matrix(1,0,0,1,234.0999755859375,133.96665954589844)"><path transform="matrix(0.017,0,0,-0.017,0,0)" d="M615 8L615 61L309 61C207 61 107 153 107 271C107 382 195 480 309 480L615 480L615 533L314 533C167 533 55 411 55 271C55 131 167 8 314 8Z" stroke="rgb(144,19,254)" stroke-opacity="1" stroke-width="8" fill="rgb(144,19,254)" fill-opacity="1"></path></g></g></g></g></g></g></g><g><g><g><text x="239.01666259765625" y="144.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">U</text></g></g></g><g><g><g><text x="338" y="92.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">Y</text></g></g></g><g><g><g><text x="286.01666259765625" y="15.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">f</text></g></g></g><g><g><g><text x="384" y="16.5" style="white-space:pre;stroke:none;fill:rgb(0,0,0);fill-opacity:1;font-size:15px;font-family:sans-serif, Arial, Helvetica;font-weight:400;font-style:normal;dominant-baseline:text-before-edge;text-decoration:rgb(0, 0, 0);">g</text></g></g></g><g><g><g><g><g><g><g><g><g><g transform="matrix(1,0,0,1,292.0333251953125,78.96665954589844)"><path transform="matrix(0.017,0,0,-0.017,0,0)" d="M345 437L329 437L350 549C366 635 394 673 440 673C470 673 497 658 512 634L522 638C527 654 537 685 545 705L550 720C534 727 503 733 480 733C469 733 453 730 445 726C421 715 339 654 316 630C294 608 282 578 271 521L256 442C215 422 195 414 170 405L165 383L246 383L237 327C207 132 170 -54 148 -123C130 -182 100 -213 64 -213C41 -213 30 -206 12 -184L-2 -188C-6 -211 -20 -259 -25 -268C-16 -273 -1 -276 10 -276C51 -276 105 -245 144 -198C215 -114 235 -18 319 383L423 383C427 402 434 425 440 439L436 446C407 439 408 437 345 437Z" stroke="rgb(0,0,0)" stroke-opacity="1" stroke-width="8" fill="rgb(0,0,0)" fill-opacity="1"></path></g></g></g></g><svg xmlns="http://www.w3.org/2000/svg" x="290.8499755859375" y="60.01666259765625" height="6" width="8.5"><defs><clipPath id="ccp0.7547762033574481"><rect x="-0.5" y="-0.5" width="9" height="6.5"></rect></clipPath></defs><g clip-path="url(#ccp0.7547762033574481)"><svg xmlns="http://www.w3.org/2000/svg" x="0" y="0" height="20.400009155273438" width="17"><defs><clipPath id="ccp0.4248055210739262"><rect x="-0.5" y="-0.5" width="17.5" height="20.900009155273438"></rect></clipPath></defs><g clip-path="url(#ccp0.4248055210739262)"><g><g transform="matrix(1,0,0,1,0,17)"><path transform="matrix(0.017,0,0,-0.017,0,0)" d="M-877 790L1877 790L1877 850L-877 850Z" stroke="rgb(0,0,0)" stroke-opacity="1" stroke-width="8" fill="rgb(0,0,0)" fill-opacity="1"></path></g></g></g></svg></g></svg></g></g></g></g></g></g><g><g><g><g><g><g><g><g><g><g transform="matrix(1,0,0,1,380.86669921875,74.96665954589844)"><path transform="matrix(0.017,0,0,-0.017,0,0)" d="M275 482C208 482 55 419 55 259C55 182 100 136 177 136C185 136 196 136 207 137L166 99C150 84 139 66 139 52C139 42 146 31 160 19C91 -4 -37 -48 -37 -159C-37 -230 33 -276 142 -276C271 -276 384 -204 384 -121C384 -85 363 -49 320 -14L260 35C226 62 213 81 213 100C213 114 228 128 240 146C338 170 409 253 409 342C409 357 406 376 405 380C422 378 430 378 439 378C456 378 466 379 489 382L498 427L495 433C467 426 447 424 393 424C393 425 369 482 275 482ZM176 1L253 -58C299 -94 318 -121 318 -154C318 -206 249 -248 166 -248C84 -248 27 -206 27 -145C27 -43 137 -11 176 1ZM250 448C308 448 337 412 337 341C337 238 289 168 219 168C158 168 126 209 126 288C126 383 177 448 250 448Z" stroke="rgb(0,0,0)" stroke-opacity="1" stroke-width="8" fill="rgb(0,0,0)" fill-opacity="1"></path></g></g></g></g><svg xmlns="http://www.w3.org/2000/svg" x="380.86669921875" y="60.01666259765625" height="6" width="10.349990844726562"><defs><clipPath id="ccp0.7888923127195474"><rect x="-0.5" y="-0.5" width="10.849990844726562" height="6.5"></rect></clipPath></defs><g clip-path="url(#ccp0.7888923127195474)"><svg xmlns="http://www.w3.org/2000/svg" x="0" y="0" height="20.400009155273438" width="17"><defs><clipPath id="ccp0.7749037550509237"><rect x="-0.5" y="-0.5" width="17.5" height="20.900009155273438"></rect></clipPath></defs><g clip-path="url(#ccp0.7749037550509237)"><g><g transform="matrix(1,0,0,1,0,17)"><path transform="matrix(0.017,0,0,-0.017,0,0)" d="M-877 790L1877 790L1877 850L-877 850Z" stroke="rgb(0,0,0)" stroke-opacity="1" stroke-width="8" fill="rgb(0,0,0)" fill-opacity="1"></path></g></g></g></svg></g></svg></g></g></g></g></g></g><g><g><g><g><g><g><g><g><g><g transform="matrix(1,0,0,1,290.86669921875,147.96665954589844)"><path transform="matrix(0.017,0,0,-0.017,0,0)" d="M345 437L329 437L350 549C366 635 394 673 440 673C470 673 497 658 512 634L522 638C527 654 537 685 545 705L550 720C534 727 503 733 480 733C469 733 453 730 445 726C421 715 339 654 316 630C294 608 282 578 271 521L256 442C215 422 195 414 170 405L165 383L246 383L237 327C207 132 170 -54 148 -123C130 -182 100 -213 64 -213C41 -213 30 -206 12 -184L-2 -188C-6 -211 -20 -259 -25 -268C-16 -273 -1 -276 10 -276C51 -276 105 -245 144 -198C215 -114 235 -18 319 383L423 383C427 402 434 425 440 439L436 446C407 439 408 437 345 437Z" stroke="rgb(0,0,0)" stroke-opacity="1" stroke-width="8" fill="rgb(0,0,0)" fill-opacity="1"></path></g></g><g><g><g><g><g transform="matrix(1,0,0,1,297.5,150.91666259765626)"><path transform="matrix(0.0119,0,0,-0.0119,0,0)" d="M179.50274 -172L179.50274 713L120.50274 713L120.50274 -172ZM1095 664L1098 692L1082 692L991 689C976 689 955 689 911 690L848 692L845 664L893 662C917 661 928 653 928 634C928 629 928 626 927 622L856 177C808 119 778 93 727 68C689 48 652 39 614 39C529 39 475 84 475 154C475 171 477 187 482 215L570 692L539 691C512 690 492 689 481 689C471 689 451 690 423 691L391 692L388 664L440 662C466 661 474 656 474 639C474 630 471 602 467 581L403 206C399 185 397 160 397 144C397 43 464 -19 576 -19C620 -19 661 -9 709 15C768 44 800 69 852 129C844 83 838 57 825 2L830 -3L852 -3L914 0C918 0 940 -1 972 -2L1004 -3L1009 25L949 29C926 31 917 37 917 50C917 65 918 70 923 106L1008 613C1014 651 1021 657 1059 661Z" stroke="rgb(0,0,0)" stroke-opacity="1" stroke-width="8" fill="rgb(0,0,0)" fill-opacity="1"></path></g></g></g></g></g></g></g><svg xmlns="http://www.w3.org/2000/svg" x="290.86669921875" y="129.01666259765625" height="6" width="20.349990844726562"><defs><clipPath id="ccp0.5976655662851303"><rect x="-0.5" y="-0.5" width="20.849990844726562" height="6.5"></rect></clipPath></defs><g clip-path="url(#ccp0.5976655662851303)"><svg xmlns="http://www.w3.org/2000/svg" x="0" y="0" height="20.400009155273438" width="17"><defs><clipPath id="ccp0.9222087023805983"><rect x="-0.5" y="-0.5" width="17.5" height="20.900009155273438"></rect></clipPath></defs><g clip-path="url(#ccp0.9222087023805983)"><g><g transform="matrix(1,0,0,1,0,17)"><path transform="matrix(0.017,0,0,-0.017,0,0)" d="M-877 790L1877 790L1877 850L-877 850Z" stroke="rgb(0,0,0)" stroke-opacity="1" stroke-width="8" fill="rgb(0,0,0)" fill-opacity="1"></path></g></g></g></svg><svg xmlns="http://www.w3.org/2000/svg" x="17" y="0" height="20.400009155273438" width="17"><defs><clipPath id="ccp0.35504595717702603"><rect x="-0.5" y="-0.5" width="17.5" height="20.900009155273438"></rect></clipPath></defs><g clip-path="url(#ccp0.35504595717702603)"><g><g transform="matrix(1,0,0,1,0,17)"><path transform="matrix(0.017,0,0,-0.017,0,0)" d="M-877 790L1877 790L1877 850L-877 850Z" stroke="rgb(0,0,0)" stroke-opacity="1" stroke-width="8" fill="rgb(0,0,0)" fill-opacity="1"></path></g></g></g></svg></g></svg></g></g></g></g></g></g></svg>
</svg>
</div>



When doing composition, we need to extract a subset $U \subseteq A$ s.t.
$$
a \in U \iff a \in  X \wedge \overline f(a) \in Y
$$
Hence the composition is determined by:
$$
\overline {g \circ f} = \overline g \circ \overline f_{|U} 
$$
with this definition, it is not hard to see that sets with partial functions indeed forms a category $\mathbf{Pfn}$.

### Left/Right R-Set

Fix $R$ as an arbitrary monoid. Define left $R$-set as a set $A$ accompanied with an **action**:
$$
\begin{aligned}
R, A \to A \newline
r,a \mapsto ra
\end{aligned}
$$
where $\forall a \in A, s, r \in R$

- $1a = a$. 
- $s(ra) = (sr)a$

The right $R$-set is defined with the action on the right side.

The left $R$-sets and right $R$-sets actually form categories, named $R$-$\textbf{Set}$ and $\textbf{Set}$-$R$ correspondingly.

The arrows are the functions such that
$$
\begin{aligned}
f(ra) & = rf(a) & ~~~ & \text {(left)}
\newline
f(ar) & = f(a)r & ~~~ & \text {(right)}
\end{aligned}
$$
Changing $R$ to a ring and $A$ to an abelian group, we can get two new categories:
$$
R-\textbf{Mod} ~~~~ \textbf{Mod}-R
$$
which are the left and right modules over $R$.

## Categories with non-function arrows

Presheaf and Complex

**[TO BE CONTINUED]**