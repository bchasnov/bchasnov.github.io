---
published: true
title: keypress global variables
layout: post
---
Keyboard input to matlab figure, modify global variable.

~~~~

global buttons;
buttons = [0 0 0];

set(gcf, 'KeyPressFcn', @(h_obj,evt) ...
 eval('global buttons; buttons = buttons + [strcmp(evt.Key,''leftarrow''), strcmp(evt.Key,''rightarrow''), strcmp(evt.Key,''q'')];'));
set(gcf, 'KeyReleaseFcn', @(h_obj,evt)...
 eval('global buttons; buttons = buttons .* [(~strcmp(evt.Key,''leftarrow'')) (~strcmp(evt.Key,''rightarrow'')) 0];'));
~~~~