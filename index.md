#### Initials and Color Transformation
-------------
For image processing, you need to pre-processing MP4 format files in double format. In this code, I used VideoReader function. I converted the RGB to YIQ space by quoting HW#2 manual’s explanation that The YIQ color space is particularly suggested for Eulerian magnification since it allows to easily amplify intensity and chromaticity independently of each other.

![image](https://user-images.githubusercontent.com/44015662/46716101-97973100-cc9d-11e8-9a27-18d1241c8515.png)![image](https://user-images.githubusercontent.com/44015662/46716103-9b2ab800-cc9d-11e8-82a5-7ab88375940d.png)

> The Y component represents the luma information, and is the only component used by black-and-white television receivers. I and Q represent the chrominance information. In YUV, the U and V components can be thought of as X and Y coordinates within the color space. I and Q can be thought of as a second pair of axes on the same graph, rotated 33°; therefore IQ and UV represent different coordinate systems on the same plane.
>   
> The YIQ system is intended to take advantage of human color-response characteristics. The eye is more sensitive to changes in the orange-blue (I) range than in the purple-green range (Q)—therefore less bandwidth is required for Q than for I. Broadcast NTSC limits I to 1.3 MHz and Q to 0.4 MHz. I and Q are frequency interleaved into the 4 MHz Y signal, which keeps the bandwidth of the overall signal down to 4.2 MHz. In YUV systems, since U and V both contain information in the orange-blue range, both components must be given the same amount of bandwidth as I to achieve similar color fidelity.
> From Wikipedia, the free encyclopedia

#### Laplacian Pyramid
-------------
First, Gaussian pyramid was obtained by down-sampling the original image twice by Gaussian filtering. Then, Laplacian Pyramid was obtained from Burt and Adelson’s The Laplacian pyramid as a compact image code (1983), the downsampled image was upsampled and the Laplacian pyramid was obtained from the error image with the next level image.

![image](https://user-images.githubusercontent.com/44015662/46716159-e218ad80-cc9d-11e8-89c6-36f6da1969b5.png)

![image](https://user-images.githubusercontent.com/44015662/46716162-e47b0780-cc9d-11e8-8446-422e6e1633a9.png)

Each level of Laplacian pyramid represents a different frequency region. For example, a smaller level represents a higher frequency.

#### Temporal Filtering
-------------
To apply the Butterworth filter, each channel of the Laplacian pyramid was transformed into frequency domain by using fft function. The specifications of the Butterworth filter are set as follows by referring to the Eulerian Video Magnification for Revealing Subtle Changes in the World.
Hd = butterworthBandpassFilter(30, 2, 2.33, 2.67)

![image](https://user-images.githubusercontent.com/44015662/46716200-03799980-cc9e-11e8-9cc8-ebe8f1831ba6.png)

A detailed description of the specification is given in Section 4.

#### Extracting the Frequency Band of Interest
-------------
It is important to extract the frequency band of interest to process to selectively amplify the blood flow. In the reference paper, the frequency band of the baby image was 2.33 Hz to 2.67 Hz. On the other hand, the face image was 0.83 Hz to 1 Hz, which can be seen in the figure below.

![image](https://user-images.githubusercontent.com/44015662/46716228-22782b80-cc9e-11e8-961d-8483c2d7735e.png)

_From Normal limits of the electrocardiogram derived from a large database of Brazilian primary care patients (June 2017)_

When converting the frequency band to heart rate, the baby is 139.8 bpm – 160.2 bpm and the face is 49.8 bpm – 60 bpm. Considering each age, it makes sense to some extent. The following figure shows the average frequency plot for Y, I, Q channel in Laplacian pyramid level 1 of baby image. (DC component is excluded)

![image](https://user-images.githubusercontent.com/44015662/46716260-45a2db00-cc9e-11e8-9802-455cab9c4584.png) ![image](https://user-images.githubusercontent.com/44015662/46716262-48053500-cc9e-11e8-8487-3dcfef09bb24.png)
![image](https://user-images.githubusercontent.com/44015662/46716267-4b98bc00-cc9e-11e8-9b5b-127041973abc.png) ![image](https://user-images.githubusercontent.com/44015662/46716268-4f2c4300-cc9e-11e8-9c92-24887a5a96cf.png)

#### Image Reconstruction
-------------
Image reconstruction was performed using filtered and inverse Fourier transformed Laplacian pyramid and stored Gaussian pyramid. A small change in the image was detected as the filtered Laplacian pyramid was multiplied by a large alpha value. However, since the high-frequency noise of the camera is amplified, the resolution of the overall image is degraded.
In this work, I used butterworthBandpassFilter(30, 2, 2.33, 2.67) for ‘baby2.mp4’ and used butterworthBandpassFilter(30, 2, 0.83, 1) for ‘face.mp4’. Using fvtool function, I found that the band of interest is best included when the order of the butterworth filter is adjusted to 2 or 6.
In the baby image, a level 1 pyramid was used to preserve the original image and a level 5 pyramid was used to highlight the blood flow in the face image.

![image](https://user-images.githubusercontent.com/44015662/46716299-653a0380-cc9e-11e8-8d4d-361a5255ade8.png)

##### Baby reference video : <https://youtu.be/bd6UpSlucno>

Baby, α=1 : <https://youtu.be/hDBs2l7YpOw>

Baby, α=5 : <https://youtu.be/av5wL-cTI6M>

Baby, α=10 : <https://youtu.be/wu8GBfaC01o>

Baby, α=50 : <https://youtu.be/Rj7_5lHio3w>

##### Face reference video :  <https://youtu.be/S784KIP8iFc>

Face : <https://youtu.be/c97VWsn165s>
