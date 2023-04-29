a) Pausable.sol
   modifier whenNotPaused() and function pauseAll() could have used the two constants declared in the contract which was declared for the same purpose.

  constants:
  ==========
  uint256 constant internal UNPAUSE_ALL = 0;
  uint256 constant internal PAUSE_ALL = type(uint256).max;
  
  modified as 
  /// @notice Throws if the contract is paused, i.e. if any of the bits in `_paused` is flipped to 1.
  modifier whenNotPaused() {
     require(_paused == UNPAUSE_ALL, "Pausable: contract is paused");
        _;
  } 


  function pauseAll() external onlyPauser {
        _paused = PAUSE_ALL;
        emit Paused(msg.sender, type(uint256).max);
  }


  