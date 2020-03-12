# Eyra benchmark for ocr

__Goal:__
* preparing algorithms that can be used to be submitted to the EYRA benchmark platform, with a page.xml as output
* search for a state-of-the art similarity measure. 

__Outcomes:__
* Two pre-processing algorithms which pre-process the images and save the new images
* A tool which can be used to run Tesseract (an OCR engine) on these images, with as output a page.xml
* A jacqueard similarity measure 

__Notes:__
* The page.xml output has changed: The format of polygons stored in <Coords> has been changed from 
	<Point> elements to a 'points' attribute. A polygon is now represented by a
	space separated list of points (x and y are comma separated). A list 
	requires at least two points (enforced by a regular expression constraint).
	Example: <Coords points="5,3 8,10 4,5"/>
		
# Outcomes		
__Algorithm 1: pre-processing the images__   
_Dependencies:_  
pillow (pip install pillow)  
pytessearch (pip install pytesseract)  
  
_Code_  
```
from PIL import Image
import pytesseract

images = [list of the images]

for i in images:
    filename = i.split('/')[-1] + "_version1.tif"
    column = Image.open(i)
    gray = column.convert('L')
    blackwhite = gray.point(lambda x: 0 if x < 100 else 255, '1')
    blackwhite.save(filename)
```

__Algorithm 2: pre-processing the images__   
_Dependencies:_  
pillow (pip install pillow)  
pytessearch (pip install pytesseract)  

```
import pytesseract
from PIL import Image, ImageFilter

images = [list of the images]

for i in images:
    filename = i.split('/')[-1] + "_version2.tif"
    im = Image.open(i)
    im = im.convert('L').resize([3 * _ for _ in im.size], Image.BICUBIC)
    im = im.point(lambda p: p > 150 and p + 100)
    im.save(filename)
```
__From images to page.xml__

* Downloaded Tesseract to Page from Prima ( http://www.primaresearch.org/tools/TesseractOCRToPAGE )
* Added a new map 'output' en renamed 'input_output' to only 'input'
* Put testdata (pre-proccesed images) in the input folder
* Created batchfile with following code:

```
::
:: Autodetect Executable
::

FOR %%c in ("bin\Tesseract*.exe") DO (
 SET EXE=%%c
)


echo.>log.txt

::
:: Loop through all files within the specified folder
::

FOR %%c in ("input\*.tif") DO (
  %EXE% -inp-img "%%c" -out-xml "output\%%~nc.xml" -rec-mode ocr-regions -lang nld -orig-outlines>>log.txt
)
```

Adjusted version is available as a zip file. 


__Calculating document similarity__

The intersection() method returns a set that contains the similarity between two or more sets. Meaning: The returned set contains only items that exist in both sets, or in all sets if the comparison is done with more than two sets.  
  
Output: a number between zero (no similarity) and hundred (completly similar)

```
def get_jaccard_sim(str1, str2): 
    a = set(str1.split()) 
    b = set(str2.split())
    c = a.intersection(b)
    return float(len(c)) / (len(a) + len(b) - len(c))
   
gt_text = 'Plain ground truth text'
ocr_text = 'Plain ocred text'
    
get_jaccard_sim(gt_text,ocr_test)    
```

|               | algoritme 1   | algrotime 2   |
| ------------- |:-------------:| -----:        |
| 529526     	| 0,44		| 0,55		|
| 529527     	| 0,52     	| 0,59 		|
| 529528 	| 0,4    	| 0,56		|
| 529529	| 0,4		| 0,59		|
| 529530	| 0,09		| 0,5		|


