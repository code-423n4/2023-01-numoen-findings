
## #1 Redundant Updating of the variable `sizeNext`
file: src/core/libraries/Position.sol

### Description 
In line # 57 of the update() function if the condition `sizeDelta == 0` is true and the  condition `_positionInfo.size == 0` is false then the _positionInfo.size is stored in the variable `sizeNext`. This storing and updating is redundant and is wasting gas because if the sizeDelta is zero then sizeNext is not used anywhere else in the code. The code at line 57 should be removed.





