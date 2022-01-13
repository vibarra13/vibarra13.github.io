## Welcome to my project portfolio

This portfolio is to showcase some coding projects that I have come up with. 

• First is the website that I coded with HTML and CSS, which is to showcase my coding skills and the astrophotography photos that I have taken over the last few years.

• Second is a data astronomy project that I worked on from a class in data astronomy on Coursera. With some guidance, I was able to create a crossmatching algorithm that
referenced two radio telescope catalogs of space objects. After referencing those catalogs, the algorithm finds one item from one catalog, and references all items from the
other catalog to see if they are the same. This is done for both catalogs, and the function returns the matches, and even returns the non-matches. This project helped me learn python's packages AstroPy to read FITS image files and NumPy to make usable data. I learned how to create a viable algorithm to cross reference space catalogs, how to work with large data files to create usable data, and print out easy to read results.

### Cross-matching algorithm

In order to see how the algorithm works, I need to demonstrate the files that contain the data. They are from the SuperCOSMOS radio telescope catalog, and the BSS (AT20G Astronomical radio catalog) catalog. Both of the catalog files contain information about their ID number and their position in the celestial sphere in Right Ascension & Declination. There is also other data contained within the files but I took only the data I needed using array segmentation. Here is a sample of that data:

```markdown
Syntax highlighted code block

  1  00 04 35.65 -47 36 19.1   0.87 0.04 0.97 0.06  0.90 0.04                0.995 0.030            17.63 Q 1.F.11.C  PKS 0002-478
  2  00 10 35.92 -30 27 48.3   0.74 0.03 0.72 0.04  0.63 0.03  0.315 0.009   0.419 0.013 1.19  La01 19.59 Q 1.F.11..  PKS 0008-307
  3  00 11 01.27 -26 12 33.1   0.64 0.07 0.82 0.07  0.69 0.03  0.210 0.006               1.096 Wr83 19.53 Q 4.F.44.C  PKS 0008-264

```




