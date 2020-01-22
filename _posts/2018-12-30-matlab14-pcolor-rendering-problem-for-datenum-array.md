---
title: 'matlab14 pcolor rendering problem'
date: 2018-12-30
permalink: /posts/2018/12/2018-12-30-matlab14-pcolor-rendering-problem-for-datenum-array/
tags:
  - matlab
---

When I try to display a lidar range-corrected signal for a certain day with the codes below. FYI, matlab14a is used to produce the bug.

```matlab
figure;
p = pcolor(time, height, rcs); 
set(p, 'EdgeColor', 'none');
```

<center>
<img src="{{site.url}}/images/pcolor_problem.png" alt="pcolor problem" width="500" height="400">
<br>
<b>
example
</b>
</center>

This is because of the truncation error caused by the conversion from single to double when rendering by OpenGL ([discussion](https://de.mathworks.com/matlabcentral/answers/215398-binning-problem-when-using-datenum-with-pcolor)). There are two way to get rid of this. Firstly, set a proper renderer when saving the figure. The other one is using **rem(time, 1)** to make the interval compatible with the xscale. Both ways work for me.