
**主要用pytorch中的torchvision.transforms类**


数据增强的主要方法

1. 几何变换
	翻转
	 旋转
	裁剪/缩放
2. 仿射变换
3. 颜色增强
4. 模糊/噪声
5. 遮挡
6. mixup/cutmix

**主要的接口有**
```python
transforms.Resize(size)//调整图片尺寸
```

```cpp
transforms.RandomResize(min_size,max_size)//随机改变尺寸
```

```cpp
transforms.RandomResizedCrop()//随机裁剪+Resize
```

........等等

总结来说，这八类数据增强方法接口api的查找方法

**总思想：要去torchvision.transforms类里找
**常用的是随机数据增强，所以也可以在下面单词前面加Random找接口**

1. 翻转：Flip
2. 旋转：Rotation
3. 裁剪：Crop
4. 缩放：Resize
5. 仿射变换：Affine
6. 透视变换（模拟相机从不同角度观察目标）：Perspective
7. 颜色增强：Color
8. Mixup:核心思想：x3=a*x1+（1-a）*x2
9. 遮挡擦除：Erasing

...


**仿射变换和透视变换的区别**：==仿射变化类似原地观察，目标移动，不会改变目标原本两条平行边的位置关系，而透视变换模拟的是相机的视角变化，会产生近大远小的结果，原来平行的两条边可能不再平行
