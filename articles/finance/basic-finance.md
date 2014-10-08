
# Finance Notes


## Measuring Interest Rates


### Effect of compounding frequency on the value of $100 at the end of 1 year

| Compounding f       | Value of $100 at EoY |
| ------------------- |:--------------------:|
| Annually (m=1)      | 110.00               |
| Semiannually (m=2)  | 110.25               |
| Quaterly (m=4)      | 110.38               |
| Monthly (m=12)      | 110.47               |
| Weekly (m=52)       | 110.51               |
| Daily (m=360)       | 110.52               |


Supposed an amount A is invested for n years at an interest rate of R per annum. I f the rate is compounded once per annum, the terminal value of the investment is

    A(1+R)^n

If the rate is compounded m times, the terminal value is

    A(1+R/m)^m.n

### Continuous Compounding

    A.e^R.n

    100e^0.1 = $110.52


### conversion equation

    A*e^(Rc*n) = A*(1+Rm/m)^m*n

    Rc = m*ln(1+Rm/m)

    Rm = m*(e^(Rc/m) - 1)

### Bond pricing

Treasury sero rates <=> yield curve

| Maturity (years) | Zero Rate (%) (cont. compounded) |
| ---------------- | -------------------------------- |
| 0.5              | 5.0                              |
| 1.0              | 5.8                              |
| 1.5              | 6.4                              |
| 2.0              | 6.8                              |


Now price a 2-year Treasury bond with a principal of $100, provides  coupons at the rate of 6% per annum semiannually.

cash flow is

    $3 -> $3 -> $3 -> $103

or

| time (year) | cash flow |
| ----------- | --------- |
| 0.5         | $3        |
| 1           | $3        |
| 1.5         | $3        |
| 2           | $103        |

Let's price this cash flow:

    sum(cashFlow[i].discount_factor[i])

    discount_factor = exp(-yieldCurve-rate * time)

    3*e^-0.05*0.5 + 3*e^-0.058*1.0 + 3^-0.064*1.5 + 103*e^-0.068*2 = $98.39

### Bond yield

    3*e^-y*0.5 + 3*e^-y*1.0 + 3^-y*1.5 + 103*e^-y*2 = $98.39

    y = 6.76%

### Par Yield


    (c/2)*e^-0.05*0.5 + (c/2)*e^-0.058*1.0 + (c/2)^-0.064*1.5 + (100 + c/2)*e^-0.068*2 = $100

    m = 2

    c = 6.87%  (6.75% continuous compounding)

More generally, if d is the present value of $1 received at the maturity of the bond, A is the value of an annuity that pays one dollar on each coupon payment datem and m is the number of coupon payments per year, then the par yield c must satisfy

    100 = A*c/m+10d

or

    c = (100 -100d)*m/A

in our example m = 2, d = e^-0.068*2

