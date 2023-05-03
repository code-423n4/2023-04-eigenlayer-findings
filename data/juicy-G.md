# src/contracts/permissions/Pausable.sol
## R=73: Gas savings from utilising revert instead of require
The function should use an “if” statement instead of a “require”, as this is more gas efficient. I propose the following change to the code:

```
if((_paused & newPausedStatus) == _paused):
	_paused = newPausedStatus;
	emit Paused(msg.sender, newPausedStatus);
else:
	revert("Pausable.pause: invalid attempt to unpause functionality”)
```

The same principle can be applied to lines 33, 38, 44, 50, 56, and 94 as well. 