# SurplusEconomic
********************************************Begin Code******************************************

*****Modelo Base Line
**** importar datos de parametrosSocioecononmicos
clear all
import excel "C:\Workspace\ModelSurplusForages\ParametrosSocioeconomicos.xlsm", sheet("StataAdoption") firstrow

save "C:\Workspace\ModelSurplusForages\AdoptionCurves\\dataadoption.dta", replace
**** parameter by country

///value1 valor del pico que se obtiene en el time1
set more off

gen z=.
replace z=1 if id=="Kenya" 
replace z=2 if id=="Tanzania" 
replace z=3 if id=="Ethiopia"
replace z=4 if id=="Uganda"
replace z=5 if id=="Rwanda"
replace z=6 if id=="Burundi"
label define z 1"Kenya" 2"Tanzania" 3"Ethiopia" 4"Uganda" 5"Rwanda" 6"Burundi"
label value z z  
tab z
save "dataadoption.dta", replace
clear


////segundo intento
use dataadoption.dta, clear
local v `""Kenya" "Tanzania" "Ethiopia" "Uganda" "Rwanda" "Burundi""'
 foreach var of local v {
 keep if id=="`var'"
 save "`var'.dta", replace
 clear
 use dataadoption.dta, clear
 }
 *end
 clear
 
 
 set more off
 local path C:\Workspace\ModelSurplusForages\AdoptionCurves\
foreach name_n in Kenya Tanzania Ethiopia Uganda Rwanda Burundi{
use "`path'\`name_n'.dta",  clear
	
	
	*** adoption rate
	gen adoptRateBase= AdoptionAtmax/[1+exp(-(alfaBase+(betaBase*time)))]
	gen adoptRateSpatial= AdoptionAtmax/[1+exp(-(alfaSpatial+(betaSpatial*time)))]
	gen adoptRateindex= AdoptionAtmax/[1+exp(-(alfaindex+(betaindex*time)))]

	*** save 
	save "`name_n'.dta",  replace

	**** graph base
	# delimit 
	;
	twoway (line adoptRateBase time), title(" `name_n' Adoption Base") note("Country")
	ylabel(#10, angle(h) labsize(small))  	
	;
	# delimit cr
	graph save "C:\Workspace\ModelSurplusForages\AdoptionCurves\\adoptBase`name_n'.gph", replace
	graph export "C:\Workspace\ModelSurplusForages\AdoptionCurves\\adoptBase`name_n'.png", width(4000) replace 
	
			
	**** graph Spatial
	# delimit 
	;
	twoway (line adoptRateSpatial time), title(" `name_n' Adoption Spatial") note("Country")
	ylabel(#10, angle(h) labsize(small)) 
 	;
	# delimit cr
	graph save "C:\Workspace\ModelSurplusForages\AdoptionCurves\\adoptRateSpatial`name_n'.gph", replace
	graph export "C:\Workspace\ModelSurplusForages\AdoptionCurves\\adoptRateSpatial`name_n'.png", width(4000) replace 

		**** graph Spatial
	# delimit 
	;
	twoway (line adoptRateindex time), title(" `name_n' Adoption Index") note("Country")
	ylabel(#10, angle(h) labsize(small))  	
	;
	# delimit cr
	graph save "C:\Workspace\ModelSurplusForages\AdoptionCurves\\adoptRateindex`name_n'.gph", replace
	graph export "C:\Workspace\ModelSurplusForages\AdoptionCurves\\adoptRateindex`name_n'.png", width(4000) replace 

	rename adoptRateBase adopBase`name_n'
	rename adoptRateSpatial adopSpatial`name_n'
	rename adoptRateindex adopIndex`name_n'

	save "`name_n'.dta",  replace
 
 keep time adopBase`name_n' adopSpatial`name_n' adopIndex`name_n'
 save "`path'\adopt`name_n'.dta",  replace
 use "`path'\`name_n'.dta",  clear
 }
*end

clear


use "C:\Workspace\ModelSurplusForages\AdoptionCurves\\Tanzania.dta", clear
keep time 
save "C:\Workspace\ModelSurplusForages\graphs\\time.dta",replace 



use "C:\Workspace\ModelSurplusForages\graphs\\time.dta", clear
local path C:\Workspace\ModelSurplusForages\AdoptionCurves\
foreach name_n in adoptBurundi adoptEthiopia adoptKenya adoptRwanda adoptTanzania adoptUganda{
merge 1:1   time  using "`path'\`name_n'.dta"
drop _merge
save "C:\Workspace\ModelSurplusForages\AdoptionCurves\\m_`name_n'.dta", replace
}
*end
save "C:\Workspace\ModelSurplusForages\AdoptionCurves\\mergetotal.dta", replace 



*****grafica de las tasas de adoption linea base
use "C:\Workspace\ModelSurplusForages\AdoptionCurves\\mergetotal.dta", clear 

#delimit ;
twoway line adopBaseBurundi adopBaseEthiopia adopBaseKenya adopBaseRwanda adopBaseTanzania adopBaseUganda time ,
  ytitle("Percentage") 
  ylabel(#10, angle(h) labsize(small)) 	
  xlabel(1(1)30, angle(h) labsize(small) )
  title("Adoption Rate of Tecnology by Countries")
  subtitle("Base Line")
  note("Surplus Economics Model")
  legend(off) scheme(s1color)
  legend(label(1 "Kenya") label(2 "Tanzania") label(3 "Ethiopia")label(4 "Uganda")label(5 "Rwanda")label(6 "Burundi"))
  ;
#delimit cr
graph save "C:\Workspace\ModelSurplusForages\graphs\\AdoptionBase.gph", replace
graph export "C:\Workspace\ModelSurplusForages\graphs\\AdoptionBase.png", width(4000) replace 



************************************END CODE**********************************************************


 ****using spatial concentratrion
#delimit ;
twoway line adopSpatialBurundi adopSpatialEthiopia adopSpatialKenya adopSpatialRwanda adopSpatialTanzania adopSpatialUganda time ,
  ytitle("Percentage") 
  ylabel(#10, angle(h) labsize(small)) 	
  xlabel(1(1)30, angle(h) labsize(small) )
  title("Scenario Spatial")
  note("Surplus Economics Model") scheme(s1color)
  legend(label(1 "Burundi") label(2 "Ethiopia") label(3 "Kenya")label(4 "Rwanda")label(5 "Tanzania")label(6 "Uganda"))
  ;
#delimit cr
graph save "C:\Workspace\ModelSurplusForages\graphs\\Adoptionspatial.gph", replace
graph export "C:\Workspace\ModelSurplusForages\graphs\\Adoptionspatial.png", width(4000) replace 



  ****using index
#delimit ;
twoway line adopIndexBurundi adopIndexEthiopia adopIndexKenya adopIndexRwanda adopIndexTanzania adopIndexUganda time ,
  ytitle("Percentage") 
  ylabel(#10, angle(h) labsize(small)) 	
  xlabel(1(1)30, angle(h) labsize(small) )
  title("Scenario Profile Adoption")
  note("Surplus Economics Model") scheme(s1color)
  legend(label(1 "Burundi") label(2 "Ethiopia") label(3 "Kenya")label(4 "Rwanda")label(5 "Tanzania")label(6 "Uganda"))
  ;
#delimit cr
graph save "C:\Workspace\ModelSurplusForages\graphs\\AdoptionIndex.gph", replace
graph export "C:\Workspace\ModelSurplusForages\graphs\\AdoptionIndex.png", width(4000) replace 


 
 
*** example Kenya
 #delimit
 ;
twoway line  adopBaseKenya adopSpatialEthiopia adopIndexKenya  time ,
  ytitle("Percentage") 
  ylabel(#10, angle(h) labsize(small)) 	
  xlabel(1(1)30, angle(h) labsize(small) )
  title("Kenya")
  note("Surplus Economics Model") scheme(s1color)
  legend(label(1 "BASE") label(2 "Spatial") label(3 "Index"))
  ;
#delimit cr
graph save "C:\Workspace\ModelSurplusForages\graphs\\AdoptionKenya.gph", replace
graph export "C:\Workspace\ModelSurplusForages\graphs\\AdoptionKenya.png", width(4000) replace 
