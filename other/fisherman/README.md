# Fisherman game example

How to test:

## build component
1. `resim reset`
1. `export xrd=030000000000000000000000000000000000000000000000000004`
1. `resim publish .` -> save package id into $package
1. `resim new-account` -> save address and private key into $admin and $admin_priv
### Build component with specific fee
1. `resim call-function $package Fisherman new 2 -> save into $component and save admin badge into $admin_badge 

## Create players 
resim new-account -> save into $player1 and $p_priv1
resim new-account -> save into $player2 and $p_priv2
resim new-account -> save into $player3 and $p_priv3

## Initiate new game
Add some price per game
1. `resim call-method $component new_game 5 1,$admin_badge`

## Play
### Player 1
1. `resim set-default-account $player1 $p_priv1`
1. `resim call-method $component capture $player1 5 5,$xrd`
### Player 2
1. `resim set-default-account $player2 $p_priv2`
1. `resim call-method $component capture $player2 1 5,$xrd`
### Player 3
1. `resim set-default-account $player3 $p_priv3`
1. `resim call-method $component capture $player3 3 5,$xrd`

## Finish the game, calculate results and define winners
1. `resim set-default-account $admin $admin_priv`
1. `resim call-method $component finish 1,$admin_badge`       

 ## Admin Supporting Methods
 Withdraw some free assets 
 1. `resim call-method $component withdraw 0.2 1,$admin_badge`