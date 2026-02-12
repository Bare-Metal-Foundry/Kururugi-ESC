# Technical Brief: Selection of the induction - L

The datasheet specified that the saturation current of the inductance must be higher than 2.48A, but this would result in a physically large inductance SMD part, which we cannot afford as compactness is important. Hence, we will estimate an approximation of the all time max current output of the BUCK.

We estemeed the max current to be 590mA according the following value:
1. The LDO (STM32G484 & other): 300mA
2. The 2xGD (2EDL8X2X): 61x2 = 120mA (with a x2 security coeficient)
3. Margin (24%): 100mA



Taking some margin, we will size the buck for $I_{O,max}>522mA$. The highest operating point for such a load, considering sag, is set at 26V. Fsw = 833kHz. Following next equation, it appears that a std 22µH inductance will allows a max current ripple of ~65%.

$L_{\min} = \frac{V_O \times \left( V_{IN,\max} - V_O \right)}{V_{IN,\max} \times F_{SW}\times  I_{O,\max}\times  65\%} = 21.8\mu H$. We will use a 22µH.

Furthermore, current delta pic to pic is 

$\Delta I_L
= \frac{V_O\left(V_{IN}-V_O\right)}{V_{IN}\,L\,f_{SW}} = 336\text{mA}_{pp}$

So Ioutmax = 758mA and Ioutmin = 422mA.
