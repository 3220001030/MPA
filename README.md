# MPA
Dataset and codes for Spatiotemporal Distribution and Influencing Factors of Chinese Marine Protected Areas Using A Social-Ecological System Framework
## Data preparation
Import data into Stata.
```stata
clear all

import excel "/Users/gurumakaza/Library/CloudStorage/OneDrive-MacauUniversityofScienceandTechnology/D盘/【课程】M/【M】SPATIAL ECONOMETRICS/期末/保护区时空分布/全过程/Data.xlsx", sheet("Sheet2") firstrow clear

gen Precip = precipitation_six_hour_year/10
gen Wind = wind_speed_year/10
gen Green = 公园绿地面积_公顷_市辖区/10000
gen Traffic = 水运客运量_万人_全市/1000
gen Density = 人口密度_人每平方公里_全市/1000
gen Built = 建成区面积_平方公里_市辖区/100
gen Tech = 科研综合技术服务业_人_市辖区/10000
gen Infra = 城市环境基础设施建设本年完成投资额_万元/(1000000)
gen Culture = 每百人公共图书馆藏书_册_全市/100
gen GDP = 地区生产总值_当年价格_亿元_全市/(10000)
gen Pop = 年平均人口_万人_全市/100
gen Export = 货物出口额_万元_全市/(100000000*deflator)
gen ec = exp(c)
gen Time = Year-1998
global list "Precip Wind Green Traffic Density Built Tech Infra Culture GDP Pop Export”
```
## Descriptive statistics
```stata
asdoc tabstat Area c ec $list, stat(N mean sd min p25 p50 p75 p90 max) col(s) format(%6.3f)
```
|          |   N  |   Mean    |   SD     |   Min  |   p25  |   Median |   p75   |   p90    |   Max     |
| -------- | ---- | --------- | -------- | ------ | ------ | -------- | ------- | -------- | --------- |
|  Area    | 1127 | 1690.591  | 4083.238 | 0      | 93.588 | 327.961  | 894.52  | 3399.789 | 24000     |
|  c       | 1127 | 4.079     | 4.144    | 0      | 1.769  | 3        | 5       | 7.857    | 24        |
|  ec      | 1127 | 2.300e+08 | 2.0e+09  | 1      | 5.866  | 20.086   | 148.413 | 2584.127 | 2.600e+10 |
|  Precip  | 1127 | 2.31      | 1.430    | \-.192 | 1.215  | 2.18     | 3.214   | 4.296    | 6.989     |
|  Wind    | 1127 | 2.757     | 0.784    | 0      | 2.418  | 2.579    | 3.195   | 3.62     | 5.706     |
|  Green   | 1127 | .206      | 0.332    | .002   | .047   | .092     | .206    | .416     | 2.198     |
|  Traffic | 1127 | .531      | 2.167    | .001   | .018   | .101     | .264    | .618     | 15.315    |
|  Density | 1127 | .642      | 0.569    | .123   | .362   | .53      | .695    | 1.005    | 11.564    |
|  Built   | 1127 | 1.561     | 1.953    | .08    | .53    | .91      | 1.69    | 3.16     | 12.38     |
|  Tech    | 1127 | .833      | 2.081    | .01    | .177   | .319     | .527    | 1.564    | 30.59     |
|  Infra   | 1127 | .202      | 0.322    | 0      | .054   | .105     | .213    | .417     | 2.332     |
|  Culture | 1127 | .753      | 1.276    | .02    | .189   | .337     | .701    | 1.643    | 9.372     |
|  GDP     | 1127 | .247      | 0.396    | .002   | .048   | .115     | .273    | .614     | 3.87      |
|  Pop     | 1127 | 4.367     | 2.817    | .481   | 2.401  | 3.51     | 6.534   | 7.959    | 14.66     |
|  Export  | 1127 | .112      | 0.233    | 0      | .01    | .024     | .117    | .247     | 1.697     |
## Full regression
PPMLHDFE regression with full sample exported to ```Full.doc```.
```stata
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
## Threshold calculation
### GDP
Threshold analysis exported to ```GDP.doc```.
```stata
erase GDP.txt  
erase GDP.doc
cap erase GDP.doc  // Delete existing file before starting
cap erase GDP.txt  // Delete existing file before starting
foreach threshold of numlist 0.048(0.005)0.273 {
    ppmlhdfe Area Time $list if GDP<=`threshold', absorb(Province City) vce(robust) nolog
    outreg2 using GDP.doc, append keep(GDP) alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(`threshold') addstat(Pseudo R-squared, `e(r2_p)’)
}
```
### Population density
Threshold analysis exported to ```Density.doc```.
```stata
erase Density.txt  
erase Density.doc
cap erase Density.doc  // Delete existing file before starting
cap erase Density.txt  // Delete existing file before starting
foreach threshold of numlist 0.362(0.01)0.695 {
    ppmlhdfe Area Time $list if Density<=`threshold', absorb(Province City) vce(robust) nolog
    outreg2 using Density.doc, append keep(Density) alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(`threshold') addstat(Pseudo R-squared, `e(r2_p)’)
}
```
### Built-up area
Threshold analysis exported to ```Built.doc```.
```stata
erase Built.txt  
erase Built.doc
cap erase Built.doc  // Delete existing file before starting
cap erase Built.txt  // Delete existing file before starting
foreach threshold of numlist 0.530(0.02)1.690 {
    ppmlhdfe Area Time $list if Built<=`threshold', absorb(Province City) vce(robust) nolog
    outreg2 using Built.doc, append keep(Built) alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(`threshold') addstat(Pseudo R-squared, `e(r2_p)’)
}
```
### Export
Threshold analysis exported to ```Export.doc```.
```stata
erase Export.txt  
erase Export.doc
cap erase Export.doc  // Delete existing file before starting
cap erase Export.txt  // Delete existing file before starting
foreach threshold of numlist 0.01(0.002)0.118 {
    ppmlhdfe Area Time $list if Export<=`threshold', absorb(Province City) vce(robust) nolog
    outreg2 using Export.doc, append keep(Export) alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(`threshold') addstat(Pseudo R-squared, `e(r2_p)’)
}
```
## Threshold regression
### GDP
Threshold analysis exported to ```GDP1.doc```.
```stata
erase GDP1.txt  
erase GDP1.doc
cap erase GDP1.doc  // Delete existing file before starting
cap erase GDP1.txt  // Delete existing file before starting
scalar threshold = 0.095
ppmlhdfe Area Time $list if GDP<=threshold, absorb(Province City) vce(robust) nolog
outreg2 using GDP1.doc, replace alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(GDP<=`threshold') addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES) 
ppmlhdfe Area $list if GDP<=threshold, absorb(Province City Year) vce(robust) nolog
outreg2 using GDP1.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(GDP<=`threshold') addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES, Year FE, YES)
ppmlhdfe Area Time $list if GDP>threshold, absorb(Province City) vce(robust) nolog
outreg2 using GDP1.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(GDP>`threshold') addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES)
ppmlhdfe Area $list if GDP>threshold, absorb(Province City Year) vce(robust) nolog
outreg2 using GDP1.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(GDP>`threshold') addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES, Year FE, YES)
```
### Built-up area
Threshold analysis exported to ```Built1.doc```.
```stata
erase Built1.txt  
erase Built1.doc
cap erase Built1.doc  // Delete existing file before starting
cap erase Built1.txt  // Delete existing file before starting
scalar threshold = 0.7
ppmlhdfe Area Time $list if Built<=threshold, absorb(Province City) vce(robust) nolog
outreg2 using Built1.doc, replace alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(Built<=`threshold') addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES) 
ppmlhdfe Area $list if Built<=threshold, absorb(Province City Year) vce(robust) nolog
outreg2 using Built1.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(Built<=`threshold') addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES, Year FE, YES)
ppmlhdfe Area Time $list if Built>threshold, absorb(Province City) vce(robust) nolog
outreg2 using Built1.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(Built>`threshold') addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES)
ppmlhdfe Area $list if Built>threshold, absorb(Province City Year) vce(robust) nolog
outreg2 using Built1.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(Built>`threshold') addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES, Year FE, YES)
```
### Export
```stata
erase Export1.txt  
erase Export1.doc
cap erase Export1.doc  // Delete existing file before starting
cap erase Export1.txt  // Delete existing file before starting
scalar threshold = 0.014
ppmlhdfe Area Time $list if Export<=threshold, absorb(Province City) vce(robust) nolog
outreg2 using Export1.doc, replace alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(Export<=`threshold') addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES) 
ppmlhdfe Area $list if Export<=threshold, absorb(Province City Year) vce(robust) nolog
outreg2 using Export1.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(Export<=`threshold') addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES, Year FE, YES)
ppmlhdfe Area Time $list if Export>threshold, absorb(Province City) vce(robust) nolog
outreg2 using Export1.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(Export>`threshold') addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES)
ppmlhdfe Area $list if Export>threshold, absorb(Province City Year) vce(robust) nolog
outreg2 using Export1.doc, append alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(Export>`threshold') addstat(Pseudo R-squared, `e(r2_p)’) addtext(Province FE, YES, City FE, YES, Year FE, YES)
```
