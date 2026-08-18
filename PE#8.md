# Problem 8: Largest Product in a Series
## Task
Given the number:
```
7316717653133062491922511967442657474235534919493496983520312774506326239578318016984801869478851843858615607891129494954595017379583319528532088055111254069874715852386305071569329096329522744304355766896648950445244523161731856403098711121722383113622298934233803081353362766142828064444866452387493035890729629049156044077239071381051585930796086670172427121883998797908792274921901699720888093776657273330010533678812202354218097512545405947522435258490771167055601360483958644670632441572215539753697817977846174064955149290862569321978468622482839722413756570560574902614079729686524145351004748216637048440319989000889524345065854122758866688116427171479924442928230863465674813919123162824586178664583591245665294765456828489128831426076900422421902267105562632111110937054421750694165896040807198403850962455444362981230987879927244284909188845801561660979191338754992005240636899125607176060588611646710940507754100225698315520005593572972571636269561882670428252483600823257530420752963450
```
Find the thirteen adjacent digits in this 1000-digit number that have the greatest product. What is the value of this product?
## Explanation (ft. C++)
The naive approach is to calculate each and every products of thirteen adjacent digits, and then compare them altogether for the final result. However, it would be very computationally complex to execute this via brute-forcing only. Fortunately, there is a technique that drastically reduce the complexity of this process, going by the name of **Fixed Sliding Window**. This writeup would not delve into that technique, however, but rather brief the logic behind the solution of this problem only. 

The solution code will be divided into two parts: 
- The first is to compute the product of thirteen digits.
- The second is to "slide the window".

The first part is pretty straightforward: If there is any 0s in the string, the product would be automatically set to 0. Otherwise, just multiply accordingly.
```cpp
long long product = 1; bool is_zero = false;

for (int i = 0; i < k; i++){
    int digit = nums[i] - '0';
    if (digit == 0) is_zero = true;
    else product *= digit;
}
```
Note that we have to do `nums[i] - '0'`, since `nums[i]` is a char and the value is saved in ASCII value rather than corresponding to the actual number. For example, `nums[i] = '7' = 55`, not 7. Reducing `nums[i]` by `'0'`, however, can rectify the value. Applying to the same example: `nums[i] - '0' = '7' - '0' = 55 - 48 = 7`

The second part is a bit more perplexing, introducing a new concept:

**"A sliding window is a popular algorithmic concept used to process arrays or strings efficiently. Instead of recalculating values from scratch for every subset of data, it maintains a moving subset - or "window" - that slides step-by-step through the main data structure."**

At first, I tried to multiply and divide the ingoing and outgoing number directly into the product:
```cpp
for (int i = k; i < size; i++){
  if ((nums[i - k] - '0') != 0) product /= (nums[i - k] - '0'); else product = 1;
  product *= (nums[i] - '0');
  max_product = max(product, max_product);
}
```
This approach is corrupted, however. It only accounts the presence of incoming 0s, but overlooks the outgoing ones. What I failed to evaluate is that, when a 0 left, I do not recompute the product from scratch, but rather I need to factor in the non-zeros that are already in the string that I'm inspecting. Introducing a zero-counter that always evalute the current string would do wonders:
```cpp
int zero_counter = 0;

for (int i = k; i < size; i++){
    int outgoing = nums[i - k] - '0';
    int ingoing = nums[i] - '0';
    if (outgoing == 0) -- zero_counter; else product /= outgoing;
    if (ingoing == 0) ++ zero_counter; else product *= ingoing;
    if (zero_counter == 0) max_product = max(product, max_product);
}
```
## Full solution
```cpp
#include <bits/stdc++.h>
using namespace std;

int main(){
    string nums = "7316717653133062491922511967442657474235534919493496983520312774506326239578318016984801869478851843858615607891129494954595017379583319528532088055111254069874715852386305071569329096329522744304355766896648950445244523161731856403098711121722383113622298934233803081353362766142828064444866452387493035890729629049156044077239071381051585930796086670172427121883998797908792274921901699720888093776657273330010533678812202354218097512545405947522435258490771167055601360483958644670632441572215539753697817977846174064955149290862569321978468622482839722413756570560574902614079729686524145351004748216637048440319989000889524345065854122758866688116427171479924442928230863465674813919123162824586178664583591245665294765456828489128831426076900422421902267105562632111110937054421750694165896040807198403850962455444362981230987879927244284909188845801561660979191338754992005240636899125607176060588611646710940507754100225698315520005593572972571636269561882670428252483600823257530420752963450";
    int k = 13; int size = 1000;

    long long product = 1; bool is_zero = false;

    for (int i = 0; i < k; i++){
        int digit = nums[i] - '0';
        if (digit == 0) is_zero = true;
        else product *= digit;
    }

    long long max_product;
    if (is_zero == false) max_product = product;
    else max_product = 1;

    int zero_counter = 0;

    for (int i = k; i < size; i++){
        int outgoing = nums[i - k] - '0';
        int ingoing = nums[i] - '0';
        if (outgoing == 0) -- zero_counter; else product /= outgoing;
        if (ingoing == 0) ++ zero_counter; else product *= ingoing;
        if (zero_counter == 0) max_product = max(product, max_product);
    }

    cout << max_product;
}
```
