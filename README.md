# MPA
Dataset and codes for Spatiotemporal Distribution and Influencing Factors of Chinese Marine Protected Areas Using A Social-Ecological System Framework
## Data preparation
Import data into Stata.
```stata
clear all

import excel "/Users/gurumakaza/Library/CloudStorage/OneDrive-MacauUniversityofScienceandTechnology/D盘/【课程】M/【M】SPATIAL ECONOMETRICS/期末/保护区时空分布/全过程/Data.xlsx", sheet("Sheet2") firstrow clear
```
## Descriptive statistics
```stata
tabstat Area ec $list, stat(n mean sd min max) col(s) format(%6.3f)
```
## Full regression
PPMLHDFE regression with full sample exported to ```Full.doc```.
```stata
gen ec = exp(c)
gen Time = Year-1998
global list "Precip Wind Green Traffic Density Construct Tech Infra Culture GDP Pop Export”

ppmlhdfe Area Time $list, absorb(Province City) vce(robust) nolog
outreg2 using Full.doc, replace alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(Area) addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES) 
ppmlhdfe Area $list, absorb(Province City Year) vce(robust) nolog
outreg2 using Full.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(Area) addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES, Year FE, YES)
ppmlhdfe ec Time $list, absorb(Province City) vce(robust) nolog
outreg2 using Full.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(Count) addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES)
ppmlhdfe ec $list, absorb(Province City Year) vce(robust) nolog
outreg2 using Full.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(Count) addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES, Year FE, YES)
```
## Period analysis
Period anaysis exported to ```Period.doc```
```stata
ppmlhdfe Area Time $list if Year <2010, absorb(Province City) vce(robust) nolog
outreg2 using Period.doc, replace alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(1998-2009) addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES) 
ppmlhdfe Area $list if Year <2010, absorb(Province City Year) vce(robust) nolog
outreg2 using Period.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(1998-2009) addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES, Year FE, YES)
ppmlhdfe Area Time $list if Year >2009, absorb(Province City) vce(robust) nolog
outreg2 using Period.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(2010-2020) addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES)
ppmlhdfe Area $list if Year >2009, absorb(Province City Year) vce(robust) nolog
outreg2 using Period.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(2010-2020) addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES, Year FE, YES)
```
