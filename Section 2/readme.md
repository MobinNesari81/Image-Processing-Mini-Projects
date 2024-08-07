# Section 2: Image Cartoonization and Noise Analysis

This section focuses on two main tasks: image cartoonization and analyzing the effects of noise and filtering on image quality.

## Task 1: Image Cartoonization

We implement two different methods to transform a given image into a cartoon-like format.

### Method 1: Chalk Cartoonization

This method creates a sketch-like cartoon effect using the following steps:

```python
def chalk_cartoonize_image(image):
    gray_image = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    filtered_image = cv2.bilateralFilter(image, 9, 75, 75)
    edges = cv2.Canny(gray_image, 60, 180)
    cartoon_image = cv2.bitwise_and(filtered_image, filtered_image, mask=edges)
    return cartoon_image
```
1. Convert the image to grayscale
2. Apply bilateral filtering to reduce noise while preserving edges
3. Detect edges using the Canny edge detection algorithm
4. Create the final cartoon effect by applying a bitwise AND operation

### Method 2: Blurry Cartoonization
This method creates a smoother, more blurred cartoon effect:

```python
def blurry_cartoonize_image(image):
    gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    blur = cv2.medianBlur(gray, 5)
    edge = cv2.adaptiveThreshold(blur, 255, cv2.ADAPTIVE_THRESH_MEAN_C, cv2.THRESH_BINARY, 9, 9)
    color = cv2.bilateralFilter(image, 7, 250, 250)
    cartoon = cv2.bitwise_and(color, color, mask=edge)
    return cartoon
```

1. Convert the image to grayscale
2. Apply median blur to reduce noise
3. Detect edges using adaptive thresholding
4. Apply bilateral filtering to the original color image
5. Combine the color and edges using a bitwise AND operation

## Task 2: Noise and Filter Analysis

In this task, we analyze the effect of different noise intensities and filter degrees on image quality using two metrics: Mean Squared Error (MSE) and Peak Signal-to-Noise Ratio (PSNR).

### Test Images
- Lenna
- Cameraman
- Baboon

### Metrics Used

- **Mean Squared Error (MSE)**: Measures the average squared difference between the estimated values and the actual value. A lower MSE indicates better image quality.
    - Formula: `MSE = (1 / (m*n)) * Σ Σ [I(i,j) - K(i,j)]^2`
Where `I` is the original image, `K` is the noisy or filtered image, and `m` and `n` are the image dimensions.
- **Peak Signal-to-Noise Ratio (PSNR)**: Expresses the ratio between the maximum possible value of a signal and the power of distorting noise. A higher PSNR generally indicates better quality.
  - Formula: `PSNR = 20 * log10(MAX_I) - 10 * log10(MSE)`
Where `MAX_I` is the maximum possible pixel value of the image.

### Key Findings

- The worst effect on images was observed with a noise intensity of 9000 and a kernel size of 3 for both MSE and PSNR metrics.

- This combination resulted in the highest MSE values and the lowest PSNR values, indicating significant image degradation.

These results demonstrate the impact of high noise levels and insufficient filtering on image quality, highlighting the importance of proper noise reduction techniques in image processing.