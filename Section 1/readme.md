# Section 1: Face Detection and Image Transformations

## 1. Face Detection and Transformation

In this task, we use OpenCV Python to detect faces in an image and apply various linear and non-linear transformations to the extracted faces.

### Face Detection

We use the Haar Cascade Classifier with the `haarcascade_frontalface_default.xml` file for face detection. This classifier uses a machine learning approach where a cascade function is trained from many positive and negative images.

### Applied Transformations

After extracting each face, we apply the following transformations:

1. **Vertical Mirror Effect**: 
   Formula: `f(x, y) = f(x, h - y - 1)`
   where `h` is the height of the image.

2. **Horizontal Mirror Effect**: 
   Formula: `f(x, y) = f(w - x - 1, y)`
   where `w` is the width of the image.

3. **Vertical Flip**: 
   Formula: `f(x, y) = f(x, h - y - 1)`
   This is the same as the vertical mirror effect.

4. **Horizontal Flip**: 
   Formula: `f(x, y) = f(w - x - 1, y)`
   This is the same as the horizontal mirror effect.

5. **Mirroring**: 
   Combination of vertical and horizontal mirror effects.

6. **Rotate faces by 45 degrees**: 
    For rotation by angle θ:
    `x' = x cos θ - y sin θ`
    `y' = x sin θ + y cos θ`
    Where (x, y) is the original position and (x', y') is the position after rotation.

## 2. Color Indexing with K-means Classifier

In this task, we implement color indexing using the K-means classifier. We test this on various color spaces:

1. RGB (Red, Green, Blue)
2. HSV (Hue, Saturation, Value)
3. YCrCb (Luma, Chroma red, Chroma blue)
4. LAB (Lightness, A: Green-Red, B: Blue-Yellow)
5. XYZ (CIE 1931 color space)

### Color Space Transformations

#### RGB to HSV
```
V = max(R, G, B)
S = (V - min(R, G, B)) / V if V ≠ 0, else 0
H = {
60 * (G - B) / (V - min(R, G, B))     if V = R
120 + 60 * (B - R) / (V - min(R, G, B))     if V = G
240 + 60 * (R - G) / (V - min(R, G, B))     if V = B
}
```

#### RGB to YCrCb
First, convert RGB to XYZ, then XYZ to LAB.
```
X = 0.4124R + 0.3576G + 0.1805B
Y = 0.2126R + 0.7152G + 0.0722B
Z = 0.0193R + 0.1192G + 0.9505B
```

The K-means algorithm is then applied to cluster the colors in each color space, providing a method for color indexing and image segmentation based on color.