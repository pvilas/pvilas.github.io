---
layout: post
title: Python simbólico resolver ecuación
date: '2015-11-17T21:43:00.001+01:00'
author: Pere Vilas
tags:
- jupyter
- python
modified_time: '2015-11-17T21:43:34.089+01:00'
thumbnail: http://1.bp.blogspot.com/-c3lQpgMz5ik/VkuQxw_BiZI/AAAAAAAASFc/owbyCmNXDhs/s72-c/Screenshot%2Bfrom%2B2015-11-17%2B21%253A40%253A05.png
blogger_id: tag:blogger.com,1999:blog-9191328551308072706.post-6470202637313465681
blogger_orig_url: http://www.pvilas.com/2015/11/python-simbolico-resolver-ecuacion.html
---

Resolver esta ecuación en wolfram alpha,

<a href="http://1.bp.blogspot.com/-c3lQpgMz5ik/VkuQxw_BiZI/AAAAAAAASFc/owbyCmNXDhs/s1600/Screenshot%2Bfrom%2B2015-11-17%2B21%253A40%253A05.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;">
<img border="0" height="176" src="http://1.bp.blogspot.com/-c3lQpgMz5ik/VkuQxw_BiZI/AAAAAAAASFc/owbyCmNXDhs/s400/Screenshot%2Bfrom%2B2015-11-17%2B21%253A40%253A05.png" width="400" /></a>


... y su equivalente usando `sympy`


``` python

from sympy import symbols, var
v1,v2,v3,vo,r1,r2,r3,r4,rf=symbols('v1,v2,v3,vo,r1,r2,r3,r4,rf')
from sympy.solvers import solve
from sympy import init_printing
init_printing(use_unicode=False, wrap_line=False, no_global=True)
solve(v1/r1 + v2/r2 - ((v3*(r4/(r3+r4)))-vo)/rf, vo)
r1*r2*r4*v3 - r1*r3*rf*v2 - r1*r4*rf*v2 - r2*r3*rf*v1 - r2*r4*rf*v1
[-------------------------------------------------------------------]
                        r1*r2*(r3 + r4)

```