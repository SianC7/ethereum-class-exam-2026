# Written section

Student number:

Answer all five questions. **Maximum 120 words each.**

Full marks need specifics from your own work: your assigned values, your deployed addresses, your
numbers, your error messages, your range.

---

## Question 1 (5 marks)

Your sheet gave you two long starting price numbers rather than one. Explain why there are two,
state which of your two tokens ended up as `currency0` and how you knew, and say what would have
gone wrong if your code in TODO 2.1 had picked the other number.

**Answer:**

There were two starting prices as each token had its own starting price. 
Token B, Vaal Rewards (VAAL) was currency0 as alphaIsCurrency0() returned false, meaning token A (KARO) was currency1.
If sqrtPriceIfAlphaIsCurrency0 was returned instead of sqrtPriceIfBetaIsCurrency0 for startingSqrtPriceX96() in TODO 2.1, 
then the pool would have been opened with the wrong starting price (141727645691436568236881372928) instead of 
44289889278573927574025429040.

---

## Question 2 (5 marks)

You committed a predicted output before swapping. State the number you predicted, how you arrived
at it, and the output you actually received. Provide a short explanation of why the two numbers were different (if they were). 
(If they were the same, explain why you were able to predict it so accurately.)

**Answer:**

1 KARO (currency1) is worth 3.2 VAAL (currency 0) and amountIn is 1 token (1000000000000000000):

fee = 500/ 1,000,000 = 0.0005

Paying currency0 for currency1 so zeroForOne is true and we're paying in VAAL and receiving KARO, (1 VAAL = 0.3125 KARO)

Therefore output prediction is:

1 * 0.3125 * (1-0.0005) = 0.31234375 KARO EXPECTED (18-decimal integer format: 312343750000000000)

The predicted output of 312343750000000000 was different to the actual output of 312326299158883446, 
due to the addition of fee charges and price slippage (when price moves as you complete the swap, 
because the trade is changing the price)

---

## Question 3 (5 marks)

Quote the exact error message you hit on your first failed attempt at adding liquidity, and explain
the cause in terms of your own tick spacing and your own live tick. If your first attempt worked,
say so, then deliberately trigger one of the checks you wrote in TODO 3.1 or 3.2, quote the message
it gave, and explain what caused it.

**Answer:**

I succeeded in adding liquidity on my first attempt. I deliberately triggered the TODO 3.1 check for if the 
inputed tickLower is a multiple of TICK_SPACING (which was 10 for me). I was able to trigger it be setting the tickLower 
input value for addLiquidity() to -11841, which is not a mutiple of 10. The error message I recieved was 
"tickLower is not a multiple of the tick spacing"

---

## Question 4 (5 marks)

State the tick range you chose and why. If you had chosen a range entirely above the live tick,
explain what your Task3Liquidity contract would have done with TODO 3.2 completed correctly.
Then suppose that range-containment check were removed, with all other inputs valid: name which
of your two tokens Uniswap would have taken, which it would have left alone, and why.

**Answer:**

The tick range I chose was -11840 to -11440 so that the live tick value (-11633) was centered within the range.

Reasoning:

LiveTick = -11633

_tickSpacing = 10

-11633/ 10 = -1163.3 -> rounded down: -1164 -> -1164 * 10 = -11640

tickLower = -11640 - (10*20) = -11840

tickUpper = -11640 + (10*20) = -11440

If I had chosen a range entirely above the live tick (> -11633), the Task3Liquidity contract would have returned
"your range does not contain the live tick" and stopped as the live tick is lower than the tickLower value.

If the range-containment check was removed, Uniswap would have taken currency0 which is Token B (Vaal Rewards) and
left currency1 , which is Token A (Karoo Points) alone. The reason for this is because as the tick/ price increases, 
it becomes cheaper to buy currency1 using currency0, therefore above the live tick range is a buy order for currency 1.
Since the tick has not risen yet, the position holds only the currency 1 (token A) it's ready to spend, 
and no currency 0 (token B).

---

## Question 5 (5 marks)

Both your tokens use 18 decimals. Suppose token A had used 6 instead and token B still used 18, with the same real world
price. State what would change about the starting price number you passed in, and state what in
your pool key would be completely unaffected. Explain why the pool itself neither knows nor cares
about decimals.

**Answer:**

Since token B is currency0 and still uses 18 decimals, nothing would change for starting price number passed in as alphaIsCurrency0()
returned false (price would still be 44289889278573927574025429040). The poolId would not change as it is a hash created from the token A 
and B addresses as well as the fee and tickspacing values. None of these values are changed if token A uses 6 decimals for price, 
therefore the PoolId remains unchanged. The pool itself does not care as it will adjust Token A price based on the price ratio formula it uses 
and puts all Tokens in 18 decimal format. It also doesn't know Token A's price uses 6 decimals it isn't passed in at any time.
