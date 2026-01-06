# Builder Track Weekly Report — Week 2

__Name:__ Victor Okenwa.
__Week Ending:__ Friday 9th, 2026

This log summarizes my learning on the academy lessons 1.


## Overview 

* A __Cell__  is the basic unit of CKB.
* They are two types of cells: __LIVE (Unspent)__ and __Dead (Consumed)__ Cells
* A cell contains   __capacity__, __lock__, __type__, and __data__.
    ```js
        Cell: {
            capacity: HexString;
            lock: Script;
            type: Script;
            data: HexString;
        }
    ```
* A cell's total size for all four contents above must be less than or equal to the capacity of the cell. As shown below.
* The only way you can possess cells is by owning native tokens.
* A cell is a box, while the lock and type scripts are the two locks on the box.
* The lock script is the default lock, while the type script is an optional lock.
  
    ```js
        Script: {
        code_hash: HexString
        args: HexString
        hash_type: Uint8, there are three allowed values: {0: "data", 1: "type", 2: "data1"}
        }
    ```
* The __code_hash__ is the the hash of a certain piece of code. The __args__ is the arguments that will be transferred to the code.
* The __code_hash__ and __args__ jointly comprises a full lock: we can identify the code to be executed by using the __code_hash__ field and then send the argument __args__ to this code. The code will then be executed by the __CKB virtual machine (CKB-VM).__
* If the code was executed successfully, a “0” response will be returned indicating that the lock can be opened, if not, other error codes will be returned indicating that the unlocking failed and that means it is unauthorized.

* Locks are vital to the cell because without it anyone can use that cell and spend the money of the real owner.