---
title: homework1 bit-wise operations
tags: homework
grammar_cjkRuby: true
---

### 3)

#### A. all bits of x are 0.
> #### correct
> !x
> ! ( x && (0x1) )
> ~ ( x || 0x0 )

#### B. all bits of x's least significant byte are 1.
> #### correct
> ! ( ( x & 0xff ) ^ 0xff )
> ! ( x ^ ( x | 0xff ) )
> ! ( ~ ( x | 0xffffff00 ) )
> ! ( ~ x & 0xff )
> !! ( x & 0xff )
> ( x & 1 ) && ( x & 2 ) ... && ( x & 128 )

#### C. x's least significant bit is 0.
> #### correct
> ( ~x & 0x1 ) && 0x1
> ! ( x & 0x1 )
> ~x & 0x1

> #### wrong
> ! ( ~ ( x | 0xfffffffe )) you must be confusing yourself

#### D. there is at least one bit of x is 0.
> #### correct
> !! ( ~x )
> ~x && ~x

> #### wrong
> ~ ( x ^ 0 )

### 4)

> #### correct
> z = bis ( bic ( x, higher_bit_mask ), bic ( y, lower_bit_mask ) )
> z = bic ( bis ( y, lower_bit_mask ), bic ( low_bit_mask, x ) )


> #### ......
> z = bic ( x, higher_bit_mask ) | bic ( y, lower_bit_mask )
> z = bis ( bic ( x, lower_bit_mask ), bic ( y, higher_bit_mask ) )