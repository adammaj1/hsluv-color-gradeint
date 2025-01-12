1D discrete and continous perceptually uniform ordered color gradients rendered in [HSL](https://www.hsluv.org/)



# Examples

Gradient types:
* General types
  * continous  
  * discrete  
* Joining types ( see also in [1D RGB gradient](https://github.com/adammaj1/1D-RGB-color-gradient/blob/main/README.md#continous-gradient-manipulations))
  * no  
  * steps  
  * tubes  
  * diverging  

## HSL = [0, 100, l]


### continous
![](./images/0_100_continous_no.png  "description")   

![](./images/0_100_continous_no_2D.png  "description")   




![](./images/0_100_continous_steps.png "description")   

![](./images/0_100_continous_steps_2D.png "description")   


![](./images/0_100_continous_tubes.png "0_100_continous_diverging")  

![](./images/0_100_continous_tubes_2D.png  "description")   



![](./images/0_100_continous_diverging.png  "description")   

![](./images/0_100_continous_diverging_2D.png "description")   


### discrete
![](./images/0_100_discrete_no.png  "description")   

![](./images/0_100_discrete_no_2D.png  "description")   




![](./images/0_100_discrete_steps.png "description")   

![](./images/0_100_discrete_steps_2D.png "description")   


![](./images/0_100_discrete_tubes.png "0_100_dicrete_diverging")  

![](./images/0_100_discrete_tubes_2D.png  "description")   



![](./images/0_100_discrete_diverging.png  "description")   

![](./images/0_100_discrete_diverging_2D.png "description")   



## HSL = [60, 100, l]


### continous
![](./images/60_100_continous_no.png  "description")   

![](./images/60_100_continous_steps.png "description")   

![](./images/60_100_continous_tubes.png "")  

![](./images/60_100_continous_diverging.png  "description")   


### discrete
![](./images/60_100_discrete_no.png  "description")   

![](./images/60_100_discrete_steps.png "description")   

![](./images/60_100_discrete_tubes.png "")  

![](./images/60_100_discrete_diverging.png  "description")   








# Theory

Sequential map with varying lightness, single hue rendered in HSLuv color space. See : [Interactive Creation of Perceptually Uniform Color Maps by  M. Lambers ](https://diglib.eg.org/bitstream/handle/10.2312/evs20201048/055-059.pdf)
* h is constant
* s is constant
* l is changing between 0.0 and 100.0 ( full range). It is variable  = gradient position 



```c
// for each gradient position ( = lightness) compute hsl
for(iX=0; iX<iXmax; ++iX){    
	position = (double) iX / iXmax;
	// compute hsl color( position)
	// convert hsl to rgb
	hsluv2rgb(h, s, l, &r, &g, &b);
	// SaveColorToArray and Txt file
}

```

[Complementary color](https://en.wikipedia.org/wiki/Complementary_colors) for diverging gradient :
```c
h_complementary = 180.0+ h;
if (h_complementary > 360.0) { h_complementary -= 360.0;}
```







# How program works ?
General steps
* c program creates ppm and txt files
* Image Magic converts ppm to png ( color bars)
* gnuplot converts txt to png files ( 2D RGB color profiles ) 
* remove txt and ppm files

Steps of the C program:
* for each gradient type and h value create ppm and txt file
 * ppm file is created by saving 1D array of rgb values ( virtual 2D array)
 * txt file is a simple list of rgb values for gnuplot
  



Results: 
* there are 2 general gradient types ( continous and discrete ) 
* there are 4 gradient joining types ( no  steps  tubes  diverging)
* there are 7 values of lightnes :  0 60 120 180 240 300 360
* for each gradient there are 2 images: gradient bar and 2D RGB profile

so the program creates [112 png images](./images)
```math
2*4*7*2 = 112
```



```c
// make gradient images for each gradient type 
void MakeGradientImages(const double h, const double s){

	GradientGeneralType gType;
  	GradientJoiningType jType;
  	
	//
  	for (gType = 0; gType< gMaxType; ++gType)
  		for (jType = 0; jType < jMaxType; ++jType)
  				MakeGradientTypeImages(gType, jType, h, s);
}

// from main : for each h make gradient  images 
	do {
   		MakeGradientImages(h,s);
   		h += h_step; // next gradient 
	} while( h < h_Max );
```

## Gradient Joining Types

```c


double ModifyPosition(const double position, const GradientGeneralType gTypeVar, const GradientJoiningType jTypeVar){
	
	// input position  should be in  [0,1] range 
	double p = position; // p = local copy of position
	// if position > 1 then we have repetition of colors = periodic function = wave   
	 
	
	switch(jTypeVar){
		// simple monotone function 
		case no : { break;} // return input position without modifications
		
		// periodic waves with different joinings
		case steps : {	p = p * segments; // periodic  = change range
				p = frac(p); 
				
    				break;}
    				
		case tubes : {	p = p * segments; // periodic = change range
				int ip = (int)p;
      				p = p-ip; // fractional part 
      				if (ip % 2) {p = 1.0-p;} // reverse gradient
      				
				break;}
				
		case diverging : { 	p *= 2.0;
					if (p>1.0) {p = 2.0 - p;} // reverse gradient	 
										
					break;}
		
		default:{}
	}
	if ( gTypeVar == discrete ) { p = double2steps(p); }
	
	return p; // output in [0,1] range = modified position

}

```


## variables
* hsl color
  * h Hue. Between 0.0 and 360.0
  * s Saturation. Between 0.0 and 100.0
  * l Lightness. Between 0.0 and 100.0
* rgb color
 * r Red component. Between 0.0 and 1.0
 * g Green component. Between 0.0 and 1.0
 * b Blue component. Between 0.0 and 1.0



# How to run the code ?

```
make
```
Text output: 

```bash
make
printf "make ppm and txt files "
make ppm and txt files gcc g.c  hsluv.c -lm -Wall
./a.out
 first color : hsl = [0, 100, l]
 complemntary color : hsl = [180, 100, l]
File 0_100_continous_no.txt saved. 
File 0_100_continous_no.ppm saved. 
 first color : hsl = [0, 100, l]
 complemntary color : hsl = [180, 100, l]
File 0_100_continous_steps.txt saved. 
File 0_100_continous_steps.ppm saved. 
 first color : hsl = [0, 100, l]
 complemntary color : hsl = [180, 100, l]
File 0_100_continous_tubes.txt saved. 
File 0_100_continous_tubes.ppm saved. 
 first color : hsl = [0, 100, l]
 complemntary color : hsl = [180, 100, l]
File 0_100_continous_diverging.txt saved. 
File 0_100_continous_diverging.ppm saved. 
 first color : hsl = [0, 100, l]
 complemntary color : hsl = [180, 100, l]
File 0_100_discrete_no.txt saved. 
File 0_100_discrete_no.ppm saved. 
 first color : hsl = [0, 100, l]
 complemntary color : hsl = [180, 100, l]
File 0_100_discrete_steps.txt saved. 
File 0_100_discrete_steps.ppm saved. 
 first color : hsl = [0, 100, l]
 complemntary color : hsl = [180, 100, l]
File 0_100_discrete_tubes.txt saved. 
File 0_100_discrete_tubes.ppm saved. 
 first color : hsl = [0, 100, l]
 complemntary color : hsl = [180, 100, l]
File 0_100_discrete_diverging.txt saved. 
File 0_100_discrete_diverging.ppm saved. 
 first color : hsl = [60, 100, l]
 complemntary color : hsl = [240, 100, l]
File 60_100_continous_no.txt saved. 
File 60_100_continous_no.ppm saved. 
 first color : hsl = [60, 100, l]
 complemntary color : hsl = [240, 100, l]
File 60_100_continous_steps.txt saved. 
File 60_100_continous_steps.ppm saved. 
 first color : hsl = [60, 100, l]
 complemntary color : hsl = [240, 100, l]
File 60_100_continous_tubes.txt saved. 
File 60_100_continous_tubes.ppm saved. 
 first color : hsl = [60, 100, l]
 complemntary color : hsl = [240, 100, l]
File 60_100_continous_diverging.txt saved. 
File 60_100_continous_diverging.ppm saved. 
 first color : hsl = [60, 100, l]
 complemntary color : hsl = [240, 100, l]
File 60_100_discrete_no.txt saved. 
File 60_100_discrete_no.ppm saved. 
 first color : hsl = [60, 100, l]
 complemntary color : hsl = [240, 100, l]
File 60_100_discrete_steps.txt saved. 
File 60_100_discrete_steps.ppm saved. 
 first color : hsl = [60, 100, l]
 complemntary color : hsl = [240, 100, l]
File 60_100_discrete_tubes.txt saved. 
File 60_100_discrete_tubes.ppm saved. 
 first color : hsl = [60, 100, l]
 complemntary color : hsl = [240, 100, l]
File 60_100_discrete_diverging.txt saved. 
File 60_100_discrete_diverging.ppm saved. 
 first color : hsl = [120, 100, l]
 complemntary color : hsl = [300, 100, l]
File 120_100_continous_no.txt saved. 
File 120_100_continous_no.ppm saved. 
 first color : hsl = [120, 100, l]
 complemntary color : hsl = [300, 100, l]
File 120_100_continous_steps.txt saved. 
File 120_100_continous_steps.ppm saved. 
 first color : hsl = [120, 100, l]
 complemntary color : hsl = [300, 100, l]
File 120_100_continous_tubes.txt saved. 
File 120_100_continous_tubes.ppm saved. 
 first color : hsl = [120, 100, l]
 complemntary color : hsl = [300, 100, l]
File 120_100_continous_diverging.txt saved. 
File 120_100_continous_diverging.ppm saved. 
 first color : hsl = [120, 100, l]
 complemntary color : hsl = [300, 100, l]
File 120_100_discrete_no.txt saved. 
File 120_100_discrete_no.ppm saved. 
 first color : hsl = [120, 100, l]
 complemntary color : hsl = [300, 100, l]
File 120_100_discrete_steps.txt saved. 
File 120_100_discrete_steps.ppm saved. 
 first color : hsl = [120, 100, l]
 complemntary color : hsl = [300, 100, l]
File 120_100_discrete_tubes.txt saved. 
File 120_100_discrete_tubes.ppm saved. 
 first color : hsl = [120, 100, l]
 complemntary color : hsl = [300, 100, l]
File 120_100_discrete_diverging.txt saved. 
File 120_100_discrete_diverging.ppm saved. 
 first color : hsl = [180, 100, l]
 complemntary color : hsl = [360, 100, l]
File 180_100_continous_no.txt saved. 
File 180_100_continous_no.ppm saved. 
 first color : hsl = [180, 100, l]
 complemntary color : hsl = [360, 100, l]
File 180_100_continous_steps.txt saved. 
File 180_100_continous_steps.ppm saved. 
 first color : hsl = [180, 100, l]
 complemntary color : hsl = [360, 100, l]
File 180_100_continous_tubes.txt saved. 
File 180_100_continous_tubes.ppm saved. 
 first color : hsl = [180, 100, l]
 complemntary color : hsl = [360, 100, l]
File 180_100_continous_diverging.txt saved. 
File 180_100_continous_diverging.ppm saved. 
 first color : hsl = [180, 100, l]
 complemntary color : hsl = [360, 100, l]
File 180_100_discrete_no.txt saved. 
File 180_100_discrete_no.ppm saved. 
 first color : hsl = [180, 100, l]
 complemntary color : hsl = [360, 100, l]
File 180_100_discrete_steps.txt saved. 
File 180_100_discrete_steps.ppm saved. 
 first color : hsl = [180, 100, l]
 complemntary color : hsl = [360, 100, l]
File 180_100_discrete_tubes.txt saved. 
File 180_100_discrete_tubes.ppm saved. 
 first color : hsl = [180, 100, l]
 complemntary color : hsl = [360, 100, l]
File 180_100_discrete_diverging.txt saved. 
File 180_100_discrete_diverging.ppm saved. 
 first color : hsl = [240, 100, l]
 complemntary color : hsl = [60, 100, l]
File 240_100_continous_no.txt saved. 
File 240_100_continous_no.ppm saved. 
 first color : hsl = [240, 100, l]
 complemntary color : hsl = [60, 100, l]
File 240_100_continous_steps.txt saved. 
File 240_100_continous_steps.ppm saved. 
 first color : hsl = [240, 100, l]
 complemntary color : hsl = [60, 100, l]
File 240_100_continous_tubes.txt saved. 
File 240_100_continous_tubes.ppm saved. 
 first color : hsl = [240, 100, l]
 complemntary color : hsl = [60, 100, l]
File 240_100_continous_diverging.txt saved. 
File 240_100_continous_diverging.ppm saved. 
 first color : hsl = [240, 100, l]
 complemntary color : hsl = [60, 100, l]
File 240_100_discrete_no.txt saved. 
File 240_100_discrete_no.ppm saved. 
 first color : hsl = [240, 100, l]
 complemntary color : hsl = [60, 100, l]
File 240_100_discrete_steps.txt saved. 
File 240_100_discrete_steps.ppm saved. 
 first color : hsl = [240, 100, l]
 complemntary color : hsl = [60, 100, l]
File 240_100_discrete_tubes.txt saved. 
File 240_100_discrete_tubes.ppm saved. 
 first color : hsl = [240, 100, l]
 complemntary color : hsl = [60, 100, l]
File 240_100_discrete_diverging.txt saved. 
File 240_100_discrete_diverging.ppm saved. 
 first color : hsl = [300, 100, l]
 complemntary color : hsl = [120, 100, l]
File 300_100_continous_no.txt saved. 
File 300_100_continous_no.ppm saved. 
 first color : hsl = [300, 100, l]
 complemntary color : hsl = [120, 100, l]
File 300_100_continous_steps.txt saved. 
File 300_100_continous_steps.ppm saved. 
 first color : hsl = [300, 100, l]
 complemntary color : hsl = [120, 100, l]
File 300_100_continous_tubes.txt saved. 
File 300_100_continous_tubes.ppm saved. 
 first color : hsl = [300, 100, l]
 complemntary color : hsl = [120, 100, l]
File 300_100_continous_diverging.txt saved. 
File 300_100_continous_diverging.ppm saved. 
 first color : hsl = [300, 100, l]
 complemntary color : hsl = [120, 100, l]
File 300_100_discrete_no.txt saved. 
File 300_100_discrete_no.ppm saved. 
 first color : hsl = [300, 100, l]
 complemntary color : hsl = [120, 100, l]
File 300_100_discrete_steps.txt saved. 
File 300_100_discrete_steps.ppm saved. 
 first color : hsl = [300, 100, l]
 complemntary color : hsl = [120, 100, l]
File 300_100_discrete_tubes.txt saved. 
File 300_100_discrete_tubes.ppm saved. 
 first color : hsl = [300, 100, l]
 complemntary color : hsl = [120, 100, l]
File 300_100_discrete_diverging.txt saved. 
File 300_100_discrete_diverging.ppm saved. 
 first color : hsl = [360, 100, l]
 complemntary color : hsl = [180, 100, l]
File 360_100_continous_no.txt saved. 
File 360_100_continous_no.ppm saved. 
 first color : hsl = [360, 100, l]
 complemntary color : hsl = [180, 100, l]
File 360_100_continous_steps.txt saved. 
File 360_100_continous_steps.ppm saved. 
 first color : hsl = [360, 100, l]
 complemntary color : hsl = [180, 100, l]
File 360_100_continous_tubes.txt saved. 
File 360_100_continous_tubes.ppm saved. 
 first color : hsl = [360, 100, l]
 complemntary color : hsl = [180, 100, l]
File 360_100_continous_diverging.txt saved. 
File 360_100_continous_diverging.ppm saved. 
 first color : hsl = [360, 100, l]
 complemntary color : hsl = [180, 100, l]
File 360_100_discrete_no.txt saved. 
File 360_100_discrete_no.ppm saved. 
 first color : hsl = [360, 100, l]
 complemntary color : hsl = [180, 100, l]
File 360_100_discrete_steps.txt saved. 
File 360_100_discrete_steps.ppm saved. 
 first color : hsl = [360, 100, l]
 complemntary color : hsl = [180, 100, l]
File 360_100_discrete_tubes.txt saved. 
File 360_100_discrete_tubes.ppm saved. 
 first color : hsl = [360, 100, l]
 complemntary color : hsl = [180, 100, l]
File 360_100_discrete_diverging.txt saved. 
File 360_100_discrete_diverging.ppm saved. 
 allways free memory (deallocate )  to avoid memory leaks 
 HSLuv-C: Human-friendly HSL. See www.hsluv.org 
h = Hue is in  [0.0, 360.0] range
s = Saturation is in  [0.0, 100.0] range
l = Lightness is in  [0.0, 100.0] range. Here it is variable  = gradient position 
printf "convert text files to png "
convert text files to png gnuplot plot.gp
printf "convert all ppm files to png using Image Magic convert "
convert all ppm files to png using Image Magic convert chmod +x g.sh
./g.sh
0_100_continous_diverging.ppm
0_100_continous_no.ppm
0_100_continous_steps.ppm
0_100_continous_tubes.ppm
0_100_discrete_diverging.ppm
0_100_discrete_no.ppm
0_100_discrete_steps.ppm
0_100_discrete_tubes.ppm
120_100_continous_diverging.ppm
120_100_continous_no.ppm
120_100_continous_steps.ppm
120_100_continous_tubes.ppm
120_100_discrete_diverging.ppm
120_100_discrete_no.ppm
120_100_discrete_steps.ppm
120_100_discrete_tubes.ppm
180_100_continous_diverging.ppm
180_100_continous_no.ppm
180_100_continous_steps.ppm
180_100_continous_tubes.ppm
180_100_discrete_diverging.ppm
180_100_discrete_no.ppm
180_100_discrete_steps.ppm
180_100_discrete_tubes.ppm
240_100_continous_diverging.ppm
240_100_continous_no.ppm
240_100_continous_steps.ppm
240_100_continous_tubes.ppm
240_100_discrete_diverging.ppm
240_100_discrete_no.ppm
240_100_discrete_steps.ppm
240_100_discrete_tubes.ppm
300_100_continous_diverging.ppm
300_100_continous_no.ppm
300_100_continous_steps.ppm
300_100_continous_tubes.ppm
300_100_discrete_diverging.ppm
300_100_discrete_no.ppm
300_100_discrete_steps.ppm
300_100_discrete_tubes.ppm
360_100_continous_diverging.ppm
360_100_continous_no.ppm
360_100_continous_steps.ppm
360_100_continous_tubes.ppm
360_100_discrete_diverging.ppm
360_100_discrete_no.ppm
360_100_discrete_steps.ppm
360_100_discrete_tubes.ppm
60_100_continous_diverging.ppm
60_100_continous_no.ppm
60_100_continous_steps.ppm
60_100_continous_tubes.ppm
60_100_discrete_diverging.ppm
60_100_discrete_no.ppm
60_100_discrete_steps.ppm
60_100_discrete_tubes.ppm
OK
printf "delete all ppm and txt files "
delete all ppm and txt files rm *.ppm
rm *.txt
```









# Dependencies
* gcc
* hsluv-c: function hsluv2rgb from [HSLuv-C](https://github.com/hsluv/hsluv-c) = C implementation of [HSLuv = Human-friendly HSL](https://www.hsluv.org/)
* make 
* gnuplot: onverts txt to png files ( 2D RGB color profiles ) 
* Image Magic: converts ppm to png ( color bars)
* bash

Tested on:
* GNU Make 4.3
* GNU bash, version 5.0.17(1)
* GNUPLOT Version 5.2 patchlevel 8 
* gcc (Ubuntu 10.2.0-13ubuntu1) 10.2.0
* Version: ImageMagick 6.9.10-23 Q16 x86_64 20190101 https://imagemagick.org





# Credits
* function hsluv2rgb from [HSLuv-C](https://github.com/hsluv/hsluv-c) = C implementation of [HSLuv = Human-friendly HSL](https://www.hsluv.org/) by [Alexei Boronine](https://www.boronine.com/)
* [1D-RGB-color-gradient](https://github.com/adammaj1/1D-RGB-color-gradient)
* [ https://www.shellcheck.net/](shellcheck) by Vidar Holen

# Files



# Licence

[Licence](LICENCE)



# Git 



```git
echo "# hsluv-color-gradient" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:adammaj1/hsluv-color-gradient.git
git push -u origin main
```



```
  git clone git@github.com:adammaj1/hsluv-color-gradient.git
```

Subdirectory

```git
mkdir images
git add *.png
git mv  *.png ./images
git commit -m "move"
git push -u origin main
```
then link the images:

```txt
![](./images/n.png "description") 

```
to overwrite 
```git
git mv -f 
```

[to remove file from remote repo](https://stackoverflow.com/questions/2047465/how-can-i-delete-a-file-from-a-git-repository)
```git
git rm --cached file1.txt
git commit -m "remove"
git push -u origin main
```


local repo: ~/hsluv-c/test/t2

