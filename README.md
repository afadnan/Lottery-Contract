// SPDX-License-Identifier: GPL-3.0

pragma solidity >=0.8.2 <0.9.0;

//payable -> keyword which allow to receive eth or send eth
//constructor is a special type of function it run when we deploy the contract

contract Lottery{
    address payable[] public players;
    address public manager;
    
    constructor (){ 
        manager = msg.sender;//msg.sender represents address that is being called
    }

    receive() external payable {
        require( msg.value <= 0.1 ether); 
        players.push(payable (msg.sender));
    }
    
     function getBalance() public view returns (uint){
        require(msg.sender == manager);
        return address(this).balance;
    }

    function random() public view returns(uint){
        return uint(keccak256(abi.encodePacked(block.difficulty,block.timestamp,players.length)));
    }

    function winner() public {
        require(msg.sender == manager);
        require(players.length >= 3);

        uint r = random();
        address payable winner;

        uint index = r % players.length;
        winner = players[index];
        winner.transfer(getBalance());

        players = new address payable [](0);//resetting the lottery
    }
    
}
