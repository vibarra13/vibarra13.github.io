## Welcome to my project portfolio

This portfolio is to showcase some coding projects that I have come up with. 

• First is the website that I coded with HTML and CSS, which is to showcase my coding skills and the astrophotography photos that I have taken over the last few years.

• Second is a data astronomy project that I worked on from a class in data astronomy on Coursera. With some guidance, I was able to create a crossmatching algorithm that
referenced two radio telescope catalogs of space objects. After referencing those catalogs, the algorithm finds one item from one catalog, and references all items from the
other catalog to see if they are the same. This is done for both catalogs, and the function returns the matches, and even returns the non-matches. This project helped me learn python's packages AstroPy to read FITS image files and NumPy to make usable data. I learned how to create a viable algorithm to cross reference space catalogs, how to work with large data files to create usable data, and print out easy to read results.

## Cross-matching algorithm

In order to see how the algorithm works, I need to demonstrate the files that contain the data. They are from the SuperCOSMOS radio telescope catalog, and the BSS (AT20G Astronomical radio catalog) catalog. Both of the catalog files contain information about their ID number and their position in the celestial sphere in Right Ascension (RA) & Declination (DEC). There is also other data contained within the files but I took only the data I needed using array segmentation. Here is a sample of that data:

```markdown
Data file: the explanation of the column numbers we need (1-7)
Column: 1. ID Number of the object
        2. Hour in RA
        3. Minute in RA
        4. Second in RA
        5. Degree in DMS
        6. Minute in DMS
        7. Second in DMS

  1  00 04 35.65 -47 36 19.1   0.87 0.04 0.97 0.06  0.90 0.04                0.995 0.030            17.63 Q 1.F.11.C  PKS 0002-478
  2  00 10 35.92 -30 27 48.3   0.74 0.03 0.72 0.04  0.63 0.03  0.315 0.009   0.419 0.013 1.19  La01 19.59 Q 1.F.11..  PKS 0008-307
  3  00 11 01.27 -26 12 33.1   0.64 0.07 0.82 0.07  0.69 0.03  0.210 0.006               1.096 Wr83 19.53 Q 4.F.44.C  PKS 0008-264

```
That is a small sample of all the data in the BSS AT20G Catalog. Each row is one celestial object, with its ID number, coordinates in RA and DEC, in order. The only columns we need from each row are 1-7. I took the first three numbers (2-4) from the RA value, the second three numbers (5-7) from the DEC value, and converted them both to decimal degrees. This is how I came up with a definitive location for them on the celestial sphere. I created an angular distance formula using the Haversine equation to calculate distances between two objects, using the decimal degree values as input values.

Lastly, it was all a matter of creating a function with 2 variables to read in both the BSS and SuperCOSMOS catalogs, and a third input variable to set a threshold of how close something has to be to something else to consider it the same object. Here are the necessary equations to make this work:

```
#Converting RA values to decimal degrees
def hms2dec(h, m, s):
  #1 hr = 15deg, 1 min = 1/60hr, 1 sec = 1/3600hr
  decimal_degree = (h*15) + m/60 + s/3600
  return decimal_degree
  
#Converting DMS values to decimal degrees
def dms2dec(d, m, s):
    sign = d/abs(d)
    dec = abs(d) + m/60 + s/3600
    return sign*dec
    
#Angular distance formula, using the Haversine equation broken down into parts
def angular_dist(r1, d1, r2, d2):
    #To use the numpy trig functions, we first need to convert each value to radians
    r1 = np.radians(r1)
    d1 = np.radians(d1)
    r2 = np.radians(r2)
    d2 = np.radians(d2)

    a = np.sin(np.abs((d1-d2)/2))**2
    b = np.cos(d1)*np.cos(d2)*np.sin(np.abs(r1 - r2)/2)**2
    d = 2*np.arcsin(np.sqrt(a+b))
    #After completing the trig calculations, we can convert the final value back to degrees
    return np.degrees(d)

#The function to read in the BSS data file
def import_bss():
  coords = np.loadtxt('bss.dat', usecols=range(1,7))
  list_of_objects = []
  obj_id = 0
  for row in range(0, len(coords)):
    obj = ()
    obj_id += 1
    if coords[row][0] < 0:
      ra_deg = coords[row][0]*15 - coords[row][1]*0.25 - coords[row][2]*0.0041666666666667
    else:
      ra_deg = coords[row][0]*15 + coords[row][1]*0.25 + coords[row][2]*0.0041666666666667
    if coords[row][3] < 0:
      dec_deg = coords[row][3] - (coords[row][4]/60) - (coords[row][5]/3600)
    else:
      dec_deg = coords[row][3] + (coords[row][4]/60) + (coords[row][5]/3600)
    obj = (obj_id, ra_deg, dec_deg)
    list_of_objects.append(obj)
  return list_of_objects
  
#Function that reads in the SuperCOSMOS data file
def import_super():
  coords = np.loadtxt('super.csv', delimiter=',', skiprows=1, usecols=range(0,2))
  list_of_obj = []
  obj_id = 0
  for row in range(0,len(coords)):
    obj = ()
    obj_id += 1
    ra_deg = coords[row][0]
    dec_deg = coords[row][1]
    obj = (obj_id, ra_deg, dec_deg)
    list_of_obj.append(obj)
  return list_of_obj

#The crossmatch function will compare all objects of each catalog to each other, determining how many seem to match.
def crossmatch(cat1, cat2, threshold):
  matches = []
  no_matches = []
  for id1, ra1, dec1 in cat1:
    closest_dist = np.inf
    closest_id2 = None
    for id2, ra2, dec2 in cat2:
      dist = angular_dist(ra1, dec1, ra2, dec2)
      if dist < closest_dist:
        closest_id2 = id2
        closest_dist = dist
        
    if closest_dist > threshold:
      no_matches.append(id1)
    else:
      matches.append((id1, closest_id2, closest_dist))
  return matches, no_matches
  
#This is the code to test the crossmatch function.
bss_cat = import_bss()
super_cat = import_super()

max_dist = 40/3600
matches, no_matches = crossmatch(bss_cat, super_cat, max_dist)
print(matches[:3])
print(no_matches[:3])
print(len(no_matches))

max_dist = 5/3600
matches, no_matches = crossmatch(bss_cat, super_cat, max_dist)
print(matches[:3])
print(no_matches[:3])
print(len(no_matches))

```

## My Astronomy Website

I coded my own website to demonstrate what I'm able to do with CSS and HTML, and to display some of my astrophotography photos that I have imaged and processed myself.

This project took about a year and a half to complete, as I was a bit on and off with it. It was a fun project to create, and I hope to get more experience with stuff like this soon! [Click Here](https://vibarra.github.io) to see the website in its entirety, and enjoy scrolling through it! It's my pride and joy! :)


