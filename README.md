# eyra_benchmark_ocr

__Goal:__
* preparing algorithms that can be used to be submitted to the EYRA benchmark platform, with a page.xml as output
* search for a state-of-the art similarity measure. 

__Outcomes:__
* Two pre-processing algorithms which pre-process the images and save the new images
* A tool which can be used to run Tesseract (an OCR engine) on these images, with as output a page.xml
* A jacqueard similarity measure 

__Next steps:__
* Add the algorithms and tool to different Docker containers which can then be used to implement or submit on the Eyra platform. 

__Notes:__
* The page.xml output has changed: The format of polygons stored in <Coords> has been changed from 
	<Point> elements to a 'points' attribute. A polygon is now represented by a
	space separated list of points (x and y are comma separated). A list 
	requires at least two points (enforced by a regular expression constraint).
	Example: <Coords points="5,3 8,10 4,5"/>
		
		
__Algorithm 1: pre-processing the images__   
_Dependencies:_  
pillow (pip install pillow)  
pytessearch (pip install pytesseract)  
  
_Code_  
```
from PIL import Image
import pytesseract

column = Image.open('Path to image')
gray = column.convert('L')
blackwhite = gray.point(lambda x: 0 if x < 100 else 255, '1')
blackwhite.save("00529526_ocr2.tiff")
```

__Algorithm 2: pre-processing the images__ 
_Dependencies:_
pillow (pip install pillow)  
pytessearch (pip install pytesseract)  

```
import pytesseract
from PIL import Image, ImageFilter

im = Image.open('Boeken/tif/impact_boeken.tif/boeken/00529526.tif')
im = im.convert('L').resize([3 * _ for _ in im.size], Image.BICUBIC)
im = im.point(lambda p: p > 150 and p + 100)
im.save("00529526_ocr3.tiff")
```
#To do:  bovenstaande code voor meerdere images maken

__From images to page.xml__

#To do:  aanvullen

__Calculating document similarity__

```
def get_jaccard_sim(str1, str2): 
    a = set(str1.split()) 
    b = set(str2.split())
    c = a.intersection(b)
    return float(len(c)) / (len(a) + len(b) - len(c))
   
gt_text = 
ocr_text = 
    
get_jaccard_sim(gt_text,ocr_test)    
```


