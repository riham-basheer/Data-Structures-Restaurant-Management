# Restaurant Management

Project of CIE 205 - Data Structures and Algorithms Course - Fall 2019.

## Data Structures Used

All data structures are implemented in the Generic_DS file. No Order exists in two lists at the same time.
Same goes for Cooks. A Cook can be in only one of the lists.

### Cooks 

* All cooks are stored in an array of pointers to Cook object. To get the wanted list we call,

```
AllCooks[CookType][CookStatus];
// CookType -> Normal, Vegan or VIP.
// CookStatus -> FREE or BUSY
```

* Cooks who are currently during their break time are stored in a seperate list.

```
breakList -> also a vector of Cook pointers.
```

### Orders

* **Waiting Orders**: stored in three places according to their type.

```
Norm_Ord -> [Linked List]
Veg_Ord -> [Queue]
VIP_Ord -> [Priority Queue]
```

* **In-Service Orders**: A *vector* of *pairs* contains all the orders currently in service as the first data element and the
ID of the assigned cook as the second data element. 

```C++
inService[i].First   // Order pointer.
inService[i].Second  // ID of assigned cook.
```

* **Finished Orders**: They are all saved in a Linked List ordered by their Finishing Time [FT] or their Serving Time [ST].

---
## Events
The events read from the input file, may be one of three types:

### Arrival Event
Handling the arrival event is by adding the arrived order to its corresponding list. The functions [ AddOrder(Order* ) ](https://github.com/riham-basheer/DS_Project/blob/f10c79ec4e08ff9c6c75f26219c1f8187306cf74/DS%20Project/Rest/Restaurant.cpp#L189-L210) 
handles this task.

### Cancellation Event
A cancellation event is handled using two functions:
* [ getNrmOByID(int i) ](https://github.com/riham-basheer/DS_Project/blob/f10c79ec4e08ff9c6c75f26219c1f8187306cf74/DS%20Project/Rest/Restaurant.cpp#L274-L284 ): searches for an order
in the waiting list by ID.
* [ cancelOrder(int i) ](https://github.com/riham-basheer/DS_Project/blob/f10c79ec4e08ff9c6c75f26219c1f8187306cf74/DS%20Project/Rest/Restaurant.cpp#L287-L311) : utilizes the last function to search for the order in the Waiting list. If not found, it searches in the inService list. If found, the function deleted the order and frees its assigned cook by *setting its remaining time to one*.

### Promotion Event
A promo event is handled by the function,
* [ promote() ](https://github.com/riham-basheer/DS_Project/blob/f10c79ec4e08ff9c6c75f26219c1f8187306cf74/DS%20Project/Rest/Restaurant.cpp#L287-L311) : It utilizes the search 
function to find the order to be promoted. If found, the function updates its info. and add it to the VIP list.

---
## How the Simulation Works

At each Time Step, the following operations are performed.

### Check Auto Promotions

The function [ checkAutoP() ](https://github.com/riham-basheer/DS_Project/blob/f10c79ec4e08ff9c6c75f26219c1f8187306cf74/DS%20Project/Rest/Restaurant.cpp#L259-L271) loops through the normal
order's List. If there is an order waiting for longer than AutoS, it promotes it to VIP. 

### Check Finished Orders

The function responsible for the task is 
```C++
checkCooks();
```
It goes through the *Busy Cooks List*, decrementing the remaaining time of each cook. If the remaining time is 0, it means that the 
order is done. 
```C++
Cook* currCook = AllCooks[ORD_TYPE(i)][BUSY][j];
			if (!currCook->decrementRemTime(){ ....
```
##### Free the cook By:
* Finish associated order. [Using the function: [ finishOrder(Cook* asgnCook) ](https://github.com/riham-basheer/DS_Project/blob/f10c79ec4e08ff9c6c75f26219c1f8187306cf74/DS%20Project/Rest/Restaurant.cpp#L259-L271)]
* check N [number of orders Cook served].

At this point there are two possibilities:
1. *It's the cook's *nth* order and it's their break time.*
   - Set remaining time to [Break Duration].
   - Move Cook to breakList.
2. *It's not their break time yet.*
   - Move Cook to FREE List.

##### *Handling Cooks finishing their break*
looping through the breakList derementing the remaining time. If it's zero, we move the cook back to the FREE List.

### Assign Available Cooks

The function responsible for the task is,
```C++
bool serveOrders(); 
```
which loops through VIP, Vegan and Normal Lists; respectively. Checking if there is an available cook for the order in-hand using 
[ getFreeCook(ORD_TYPE) ](https://github.com/riham-basheer/DS_Project/blob/f10c79ec4e08ff9c6c75f26219c1f8187306cf74/DS%20Project/Rest/Restaurant.cpp#L363-L390) function. 
If there's one, [ AssignCook() ](https://github.com/riham-basheer/DS_Project/blob/f10c79ec4e08ff9c6c75f26219c1f8187306cf74/DS%20Project/Rest/Restaurant.cpp#L394-L414) performs the following task,
* Add the order and its assigned Cook's ID to the **inService** List.

Finally, the order is removed from its corresponding waiting list.

