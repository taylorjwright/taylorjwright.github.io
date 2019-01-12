---
layout: post
title: "Some Stata code for lead/lag event study graphs"
last_modified_at: 2019-01-11
img: assets/carr-fig3.png
---

In the fall semester I came across a (then forthcoming) paper by Jillian Carr and Jennifer Doleac titled [*Keep the Kids Inside? Juvenile Curfews and Urban Gun Violence*](https://www.mitpressjournals.org/doi/abs/10.1162/rest_a_00720) which is now published in ReStat. Figure 3 in that paper looks like this:

![Juvenile curfew effect sizes estimated for each day](/assets/carr-fig3.png)

I wanted to add a graph like this to my recent paper *Terrorism, Immigration, and Asylum Approval*. The graph didn't end up making it into the paper but recreating it was a bit tricky (for me at least) and so I wanted to provide the code so that anyone else wanting to make a similar graph would be able to.

```
// plots of coefficient estimates (quarterly)
gen sample=.
replace sample =1 if hmonth >= 488 & hmonth <= 512
gen qmonth = .
replace qmonth = qofd(hearing_date)
format qmonth %tq

// muslim-majority countries
reg a_relief ib166.qmonth##muslim_ctry $controls1 $controls0 ///
if sample == 1, coeflegend vce(cluster base_city_name2)
estimates store one

// ib166 means the 166 quarter from january 1960 is the baseline
reg a_relief ib166.qmonth##muslim_ctry $controls1 $controls0 ///
if sample == 1, coeflegend vce(cluster base_city_name2)
estimates store two

cd "$path/Work/Paper dec 2018/figures/"

coefplot (two, omitted baselevels  mcolor(black) ciopts(recast (rcap) lcolor(plb1)) levels(95) keep(*.qmonth#1.muslim_ctry)) ///
(one, omitted baselevels mcolor(black) ciopts(recast (rcap) lcolor(black)) levels(90) keep(*.qmonth#1.muslim_ctry)) ,  ///
vertical xscale(range(-1 11)) yscale(range(-0.2 0.2)) yline(0, lcolor(plr1)) nooffsets ///
ylabel(-0.2(0.1)0.2)  xtitle("Months from Sept. 11, 2001") ///
ytitle("Estimated Effect") xlabel(-1 "-18" 1 "-12" 3 "-6" 5 "0" 7 "6" 9 "12" 11 "18", valuelabels)
graph export "fig-qcoef-mvnm.eps", as(eps) replace
cd "$path/Work/dec code/"
```

The resulting graph looks like this:

![Effect sizes estimated per month](/assets/event-study-fig.png)
