# Section 3: Image Processing and Fingerprint Recognition

This section focuses on two main tasks: implementing Butterworth filters and developing a basic fingerprint recognition system.

## Task 1: Butterworth Filters

We implement both low-pass and high-pass Butterworth filters using two different approaches.

### Butterworth Low-Pass Filter

#### First Implementation

This implementation uses Fourier transform for frequency domain filtering:

```python
def butterworth_lowpass(img, n=2, d0=50):
    f = np.fft.fft2(img)
    fshift = np.fft.fftshift(f)
    rows, cols = img.shape
    crow, ccol = int(rows/2), int(cols/2)
    butterworth_lp = 1 / (1 + ((np.sqrt((np.arange(rows)[:,np.newaxis] - crow)**2 + (np.arange(cols) - ccol)**2)) / d0) ** (2*n))
    filtered_fshift = fshift * butterworth_lp
    filtered_f = np.fft.ifftshift(filtered_fshift)
    filtered_img = np.abs(np.fft.ifft2(filtered_f))
    return filtered_img
```

#### Second Implementation

This implementation uses the `scipy.signal` library:

```python
def butter_lowpass(cutoff, fs, order=5):
    nyq = 0.5 * fs
    normal_cutoff = cutoff / nyq
    b, a = butter(order, normal_cutoff, btype='low', analog=False)
    return b, a

def butter_lowpass_filter(data, cutoff, fs, order=5):
    b, a = butter_lowpass(cutoff, fs, order=order)
    y = filtfilt(b, a, data)
    return y
```

### Butterworth High-Pass Filter

#### First Implementation

This implementation uses Fourier transform for frequency domain filtering:

```python
def butterworth_highpass(img, n=2, d0=50):
    rows, cols = image.shape
    crow, ccol = rows/2 , cols/2
    mask = np.ones((rows, cols), np.float32)
    for i in range(rows):
        for j in range(cols):
            distance = np.sqrt((i-crow)**2 + (j-ccol)**2)
            mask[i,j] = 1 / (1 + (distance/d0)**(2*n))
    fimage = np.fft.fft2(image)
    fshift = np.fft.fftshift(fimage)
    fhighpass = fshift * mask
    ishift = np.fft.ifftshift(fhighpass)
    image_highpass = np.fft.ifft2(ishift)
    image_highpass = np.abs(image_highpass)
    return image_highpass
```

#### Second Implementation

This implementation uses the `scipy.signal` library:
```python
def butter_highpass(cutoff, fs, order=5):
    nyq = 0.5 * fs
    normal_cutoff = cutoff / nyq
    b, a = butter(order, normal_cutoff, btype='high', analog=False)
    return b, a

def butter_highpass_filter(data, cutoff, fs, order=5):
    b, a = butter_highpass(cutoff, fs, order=order)
    y = filtfilt(b, a, data)
    return y
```

The second implementation for both low-pass and high-pass filters provides better results due to its consideration of Fourier shifting.

## Task 2: Fingerprint Recognition

We develop a basic fingerprint recognition system using the following steps:

1. Image Preprocessing
2. Edge Extraction
3. Rotation Matrix Calculation
4. Fingerprint Classification

### Image Processing

```python
def preprocess_image(image):
    gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    _, binary = cv2.threshold(gray, 0, 255, cv2.THRESH_BINARY_INV + cv2.THRESH_OTSU)
    kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (3, 3))
    processed_image = cv2.morphologyEx(binary, cv2.MORPH_OPEN, kernel)
    return processed_image
```

### Edge Extraction

```python
def extract_edges(image):
    gradient_x = cv2.Sobel(image, cv2.CV_64F, 1, 0, ksize=3)
    gradient_y = cv2.Sobel(image, cv2.CV_64F, 0, 1, ksize=3)
    gradient_magnitude = np.sqrt(gradient_x ** 2 + gradient_y ** 2)
    _, edges = cv2.threshold(gradient_magnitude, 0, 255, cv2.THRESH_BINARY)
    return edges
```

### Rotation Matrix Calculation

```python
def get_rotation_matrix(image):
    blurred = cv2.GaussianBlur(image, (5, 5), 0)
    _, thresh = cv2.threshold(blurred, 0, 255, cv2.THRESH_BINARY_INV+cv2.THRESH_OTSU)
    contours, _ = cv2.findContours(thresh, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)
    cnt = max(contours, key=cv2.contourArea)
    x,y,w,h = cv2.boundingRect(cnt)
    cropped = image[y:y+h, x:x+w]
    moments = cv2.moments(cnt)
    hu_moments = cv2.HuMoments(moments)
    theta = -np.arctan2(hu_moments[1], hu_moments[0]) * 180 / np.pi
    center = (w//2, h//2)
    M = cv2.getRotationMatrix2D(center, int(theta), 1.0)
    return M
```

### Fingerprint Classification

The classification is based on the similarity of rotation matrices between the new fingerprint and sample fingerprints from different classes.
Note: This approach may not be highly accurate due to the high similarity between different classes of fingerprints.

```python
# Classify a new fingerprint
similarity_1 = np.sum(np.abs(M_new - M_1))
similarity_2 = np.sum(np.abs(M_new - M_2))
similarity_3 = np.sum(np.abs(M_new - M_3))
similarity_4 = np.sum(np.abs(M_new - M_4))
similarity_5 = np.sum(np.abs(M_new - M_5))

# Assign the new fingerprint to the class with the smallest difference in rotation matrix
```

This basic fingerprint recognition system demonstrates the process of preprocessing, feature extraction, and classification, but may require further refinement for practical applications.
