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

* The codes are not directly stored in the __code_hash__  but rather the __code_hash__ stored the address to the code which is stored in another cell called the __CELLDEP (Dependency Cell)__, this allows multiple cells to reference the same code.
* If the specific cell containing a lock's binary code is __CellDep__ "spent" (consumed in a transaction), the original reference in your transaction becomes invalid because cell_deps must point to live cells.
* Constructing a transaction means to destroy some cells and create new ones.
* A transaction is basically `transaction: inputs -> outputs`

* The cells in the inputs must all be live cells. The input cells will be spent and become dead cells after a transaction is committed.  Then the newly created output cells will become new live cells.

### Transaction Rules

1. The total capacity of all the output cells must be less than the total capacity of all the output cells.
2. The difference in capacity between inputs and outputs, is the fee that the miner earns. 

    ```bash
      sum(cell's capacity for each cell in inputs)
        - sum(cell's capacity for each cell in outputs)
        = fee
    ```

__Outpoint__
An Outpoint is the index of input cells. This is for __storage optimization__.
```js
OutPoint: {
  tx_hash: The hash value of the transaction to which the target cell belongs
  index: The cell position in the transaction to which the target cell belongs
}
```


### Difference between Lock Scripts and Type Script

| __Lock Script__      | __Type Script__       |
| ------------ | ---------------------- |
| Required        | Optional  |
| Protect the ownership of the box      | Ensures that the cell follows certain application logic               |
|  In a transaction, the lock scripts run for all inputs by group. | In a transaction, the type scripts run for all inputs and outputs by group.    |

## Scripts In CKB

A Script in __Nervos CKB__ is a small program that decides whether a transaction is allowed or not.

### How CKB runs a script

* CKB takes the executables and runs them in the __CKB Virtual Machine.
* After the execution, if the program returns 0 then the script ran consider successfully; any non-zero return codes will be considered Script failures. 

__Note__ When a transaction is submitted to CKB, it executes all the Scripts from the transaction to ensure that each Script succeeds. If one or more Scripts fails, the transaction will not be included on-chain.

### Types of Scripts

* __Lock Script:__ This controls who owns and access a cell.
* __Type Script:__ This controls how a cell is used in a transaction. It is like the after script.

### Script Structure

This is the structure os scripts in CKB:

```js
pub struct Script {
    pub code_hash: H256,
    pub hash_type: ScriptHashType,
    pub args: JsonBytes,
}
```
__code_hash:__ Identifies the Script code.
__hash_type:__ Defines how to interpret the code_hash when locating the Script code.
__args:__ Parameters passed to the Script, used to customize its behavior.