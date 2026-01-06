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
