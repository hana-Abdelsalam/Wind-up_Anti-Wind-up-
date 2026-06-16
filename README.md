# PID Control – Integral Wind-Up Explanation

---

## 🔹 The idea of integral wind-up

In PID we have:

`integration += error * dt`

- The integral keeps summing the error over time.

### Example:

If desired speed = 20 cm/sec  
Current speed = 0 cm/sec  
Then error = 20 cm/sec  

So the integral keeps summing:

20  
40  
60  
80  
100  

---

But in fact, the motor has a max PWM limit which is **255**.

This means that even if the PID requests **PWM = 400**,  
the motor will only take **PWM = 255**.

---

## 🔴 The problem

The integral doesn't know that the motor reached its maximum power, so it keeps accumulating.

When the speed reaches the target:

(Current velocity = 20 cm/sec)

The PWM should decrease, but the integral has already become very large, so it keeps pushing the motor hard which results in:

- Overshoot  
- Oscillations  
- Slow response  

This is called **Integral wind-up**.

---

## 🟢 The solution (Anti-Windup)

When we detect that the output has reached its maximum or minimum  
(`output > 255` or `output < 0`)

We stop the accumulation of the integral temporarily:

```cpp
if (!(saturatedHigh && error > 0) &&
    !(saturatedLow && error < 0))
{
    integration += error * dt;
}
