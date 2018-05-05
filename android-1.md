### Android 获取文字的高(宽)度

1. 获取真实宽度、高度
```
Rect rect = new Rect();
paint.getTextBounds(text,0,text.length(), rect);
width = rect.width()
height = rect.height()
```
2. 通过画笔获取宽度
```
width = paint.measureText(text,0,text.length());
```
(链接)[https://blog.csdn.net/u010661782/article/details/52805939]