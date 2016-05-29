---
published: true
title: keypress global variables 2
layout: post
---
cleaned up

~~~~
global buttons;
global b_v;

b_v = {'leftarrow','rightarrow','q'};
buttons = [0 0 0];

set(gcf, 'KeyPressFcn',   @(h_obj,e) ...
 eval('global buttons; global b_v; buttons = buttons + arrayfun(@(n) strcmp(e.Key,b_v{n}), [1:length(b_v)]);'));
set(gcf, 'KeyReleaseFcn', @(h_obj,e)...
 eval('global buttons; global b_v; buttons = buttons .* arrayfun(@(n) ~strcmp(e.Key,b_v{n}), [1:length(b_v)]);'));

~~~~