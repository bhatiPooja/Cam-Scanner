# Open CV functions descriptions step-by-step 

    image=cv2.resize(image,(1300,800)) 
    Orignal=image.copy()
#Opencv doesn't perform well with large size of image that's why we need to resize it
# copy image so that later we can display it or extract orignal image

    gray=cv2.cvtColor(image,cv2.COLOR_BGR2GRAY)
# convert colored image to Gray Sacle image

    blurred=cv2.GaussianBlur(gray,(5,5),0)
# Use Gaussian function to blur(smoothen out) our grayscale image;
#(5,5) is kernel size, a 2D matrix of 5x5 dimension would be created,this convery i/p image and dotwise matrix multi done
#0 is sigma value that determine how much blurness will take place, If 0 then it will auto calculate sigma for you

    edged=cv2.Canny(blurred,30,50)
"""Canny edge detection apply to get the edes;30 min and 50 max threshold value;
If pixle threshold max than Maximum threshold value then consider as edge and
If less than Minimum threshold value then discaded."""

    contours,hierarchy=cv2.findContours(edged,cv2.RETR_LIST,cv2.CHAIN_APPROX_SIMPLE)
"""To extract page/boundary from image use findCountor function; this function returns the image,contours and hierarchy
We only need contours here; RETR_LIST to retrieve values as list;CHAIN_APPROX_SIMPLE aproximation model is simple, 
Only necessary points are taken whereas redundent are moved
INFO: CHAIN_APPROX_NONE, this consider all points but not req here."""

       contours=sorted(contours, key=cv2.contourArea,reverse=True)
#Extract the boundaries which is the biggest contour; Need to sort in reverse order; 
#key=cv2.contourArea, so that the biggest contour is the first one.

        for c in contours:
            p=cv2.arcLength(c,True)
 #Run loop for all the contours and use arclength function which try to find square within our image/contour          
 #Ture for closed shapes where as False for open ones
 
           approx=cv2.approxPolyDP(c,0.02*p,True)
 #Approximation of whatever arcLength returns,0.02 is epsilon value adjust as per req.
    
    if len(approx)==4:#Check square or not, If return 4 points the square
        target=approx#store it in saparate variable then break the loop
        break



                def endPoints(pts): 
                    pts = pts.reshape((4,2))
                    rect = np.zeros((4, 2), dtype = "float32")
                    s = pts.sum(1)
                    rect[0] = pts[np.argmin(s)]
                    rect[2] = pts[np.argmax(s)]
                    diff = np.diff(pts, axis = 1)
                    rect[1] = pts[np.argmin(diff)]
                    rect[3] = pts[np.argmax(diff)]
                    return rect
# to find the endpoints of the square use below function
#https://www.pyimagesearch.com/2014/08/25/4-point-opencv-getperspective-transform-example/

    approx=endPoints(target)
#store endpoints of the image

    wind=np.float32([[0,0],[800,0],[800,800],[0,800]])
#Define boundary for final output to be display basicalliy window size

    
    op=cv2.getPerspectiveTransform(approx,wind)
#(EndPoints,windowSize)

    dst=cv2.warpPerspective(Orignal,op,(800,800))
#(Orignal image,o/p of getPerspectiveTransform, windowSize)



    cv2.drawContours(image, [target], -1, (0, 255, 0), 2)
    cv2.imshow("Outline", image)
    cv2.waitKey(0)
    cv2.destroyAllWindows()
    cv2.imshow("Title",dst)
    cv2.waitKey(0)
    cv2.destroyAllWindows()
