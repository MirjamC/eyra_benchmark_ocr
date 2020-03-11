# eyra_benchmark_ocr

Goal: 
* preparing algorithms that can be used to be submitted to the EYRA benchmark platform, with a page.xml as output
* search for a state-of-the art similarity measure. 

Outcomes:
* Two pre-processing algorithms which pre-process the images and save the new images
* A tool which can be used to run Tesseract (an OCR engine) on these images, with as output a page.xml
* A jacqueard similarity measure 

Next step:
* Add the algorithms and tool to different Docker containers which can then be used to implement or submit on the Eyra platform. 

Notes:
* The page.xml output has changed: The format of polygons stored in <Coords> has been changed from 
	<Point> elements to a 'points' attribute. A polygon is now represented by a
	space separated list of points (x and y are comma separated). A list 
	requires at least two points (enforced by a regular expression constraint).
	Example: <Coords points="5,3 8,10 4,5"/>
		
		
Algorithm 1: pre-processing the images
Dependencies:
pillow (pip install pillow)
pytessearch (pip install pytesseract)

Code
'from PIL import Image
import pytesseract

column = Image.open('Boeken/tif/impact_boeken.tif/boeken/00529526.tif')
gray = column.convert('L')
blackwhite = gray.point(lambda x: 0 if x < 100 else 255, '1')
blackwhite.save("00529526_ocr2.tiff")'
    
