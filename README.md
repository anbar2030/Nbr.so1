# Nbr.so1
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract ANBAR is ERC20, Ownable {
    uint256 public taxPercent = 2;
    address public taxWallet;

    constructor() ERC20("ANBAR", "NBR") {
        _mint(msg.sender, 2_000_000 * 10 ** decimals());
        taxWallet = msg.sender;
    }

    function setTaxWallet(address _wallet) external onlyOwner {
        require(_wallet != address(0), "Invalid address");
        taxWallet = _wallet;
    }

    function setTaxPercent(uint256 _percent) external onlyOwner {
        require(_percent <= 10, "Max tax is 10%");
        taxPercent = _percent;
    }

    function _transfer(address sender, address recipient, uint256 amount) internal override {
        if (taxPercent > 0 && sender != owner()) {
            uint256 tax = (amount * taxPercent) / 100;
            super._transfer(sender, taxWallet, tax);
            super._transfer(sender, recipient, amount - tax);
        } else {
            super._transfer(sender, recipient, amount);
        }
    }
}