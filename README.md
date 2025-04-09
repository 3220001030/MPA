# MPA
Dataset and codes for Spatiotemporal Distribution and Influencing Factors of Chinese Marine Protected Areas Using A Social-Ecological System Framework
## Data preparation
Import data into Stata.
```stata
clear all

import excel "/Users/gurumakaza/Library/CloudStorage/OneDrive-MacauUniversityofScienceandTechnology/D盘/【课程】M/【M】SPATIAL ECONOMETRICS/期末/保护区时空分布/全过程/Data.xlsx", sheet("Sheet2") firstrow clear

gen Precip = precipitation_six_hour_year/10
gen Wind = wind_speed_year/10
gen Green =公园绿地面积_公顷_市辖区/10000
gen Traffic = 水运客运量_万人_全市/1000
gen Density = 人口密度_人每平方公里_全市/1000
gen Built =建成区面积_平方公里_市辖区/100
gen Tech = 科研综合技术服务业_人_市辖区/10000
gen Infra = 城市环境基础设施建设本年完成投资额_万元/(1000000)
gen Culture = 每百人公共图书馆藏书_册_全市/100
gen GDP = 地区生产总值_当年价格_亿元_全市/(10000)
gen Pop = 年平均人口_万人_全市/100
gen Export = 货物出口额_万元_全市/(100000000)
```
## Descriptive statistics
```stata
tabstat Area ec $list, stat(n mean sd min max p25 p50 p75) col(s) format(%6.3f)
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
## Threshold analysis
### Population density
Threshold analysis exported to ```Density.doc```.
```stata
erase Density.txt  
erase Density.doc
cap erase Density.doc  // Delete existing file before starting
cap erase Density.txt  // Delete existing file before starting
foreach threshold of numlist 0.2(0.01)0.8 {
    ppmlhdfe Area Time $list if Density<=`threshold', absorb(Province City) vce(robust) nolog
    outreg2 using Density.doc, append keep(Density) alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(if Density<=`threshold') addstat(Pseudo R-squared, `e(r2_p)’)
}
```
### Built-up area
Threshold analysis exported to ```Density.doc```.
```stata
erase Density.txt  
erase Density.doc
cap erase Density.doc  // Delete existing file before starting
cap erase Density.txt  // Delete existing file before starting
foreach threshold of numlist 0.2(0.01)0.8 {
    ppmlhdfe Area Time $list if Density<=`threshold', absorb(Province City) vce(robust) nolog
    outreg2 using Density.doc, append keep(Density) alpha(0.001, 0.01, 0.05) bdec(3) tdec(3) ctitle(if Density<=`threshold') addstat(Pseudo R-squared, `e(r2_p)’)
