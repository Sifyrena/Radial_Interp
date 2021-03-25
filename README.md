# Radial_Interp
A pedagogical tool for radial intensity averaging.

# Required Packages:

numpy, matplotlib, scipy. A recent distribution of Python3, and Jupyter. A callable function may be added in the future.


# Plotting Radial Intensity Profile of A Concentric Circle Pattern


ywan (December 2019)


```python
import matplotlib.pyplot as plt
import numpy as np
from scipy.interpolate import RegularGridInterpolator as RPI
```

## Generate Image


```python
xArray = np.arange(1200) # Pixel Width

yArray = np.arange(800) # Pixel Height

ImX,ImY = np.meshgrid(xArray,yArray,sparse = False, indexing = 'xy')

ImageCtr = (770,419) # X,Y Pixels of the center. # Start Counting From Top Left, Computer Default
 


RArray = np.sqrt((ImX-ImageCtr[0])**2 + (ImY-ImageCtr[1])**2)

Image = np.sin(0.3*(RArray/50+1)**2) + 0.1 * np.random.random(RArray.shape) # With Noise


plt.figure(figsize=(10,10))
plt.imshow(Image, origin = 'upper',cmap = 'copper') 
```




    <matplotlib.image.AxesImage at 0x7fb3daae4dd8>




![png](output_3_1.png)


## Quick Slices


```python
# Horizontal Slice

plt.figure()

plt.plot(yArray,Image[:,ImageCtr[1]])
```




    [<matplotlib.lines.Line2D at 0x7fb3d8c5cbe0>]




![png](output_5_1.png)



```python
# Horizontal Slice

plt.figure()

plt.plot(xArray,Image[ImageCtr[0],:])


```




    [<matplotlib.lines.Line2D at 0x7fb3d8c44c88>]




![png](output_6_1.png)


## Establish Interpolator


```python
ImageInterp = RPI((yArray,xArray), Image, fill_value = 0, bounds_error = False)
```

## Interpolate and Plot

If what you are looking is out of bounds, a value of zero will be returned.


```python
N = 20 # Number of Radial Cuts
M = 400 # Number of Data Points

Stepsize = 1 # Pixels 

TestArrayTotal = np.zeros(M)
TestArray = np.zeros(M)

for i in range(N):
    
    angle = 2*np.pi/N*i
    
    UnitX = np.cos(angle)
    UnitY = np.sin(angle)
    
    PointsT = np.arange(M)
    PointsX  = PointsT*UnitX*Stepsize + ImageCtr[0]
    PointsY = PointsT*UnitY*Stepsize + ImageCtr[1]
    
    for j in range(M):
        TestArray[j] = ImageInterp([PointsY[j],PointsX[j]])
    

    TestArrayTotal += TestArray
    
plt.xlabel('Pixel Samples')
plt.ylabel('Nominal Intensity')
plt.plot(TestArrayTotal)
```




    [<matplotlib.lines.Line2D at 0x7fb3da8b5c88>]




![png](output_10_1.png)

