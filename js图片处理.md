# 图片转化

主要使用 js 将图片转为 `彩色图` 转为 `黑白图` 或 `灰色图` , 也可以用于 `提取图片轮廓`

- 获取图片数据

  ```javascript
  img.onload = () => {
    const canvas = document.createElement("canvas");
    const ctx = canvas.getContext("2d");
    canvas.width = img.width;
    canvas.height = img.height;
    ctx.drawImage(img, 0, 0);
    let imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
    handle(imageData);
  };
  ```

- 图片数据: 通过 canvas 获取到的图片数据为一个数组 `[r, g, b, a, r, g, b, a......]` , 其中 rgb 为色彩值, a 为透明度

- 数据处理
  - 灰化处理: 通过将 rgb 值取平均值分别设置到 rgb 上，即可实现图片灰化
  - 黑白化: 将图片的 rgb 值取均值后, 设置一个中间值, 将 `平均值` 大于 `中间值` 的设置为 255, 其余设置为 0
  - 轮廓化:
    - 灰化后将像素点与 `右和下` 的点比较, 取一个范围值, 点的色值差在某个范围内, 可以认定为同色点, 色值设置为 255 即可, 非同色点设置为 0 即为轮廓点
    - 也可以用彩色图片转化, 比较 rgb 值, 每个值色差都在 范围内即为同色点
