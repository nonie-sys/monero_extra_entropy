# monero_extra_entropy
## Intro
Computers are pretty bad at creating random numbers. These can be easily predicted. For this reason we need some outside event to create an entropy pool that can be used to create real random numbers.

We use a pair of hexadecimal dice to make sure the data for our entropy pool is created from real random output. A good random number is important when creating a new monero wallet. We do *not* want anyone to predict the mnemonic seed from our private key.

![alt text](https://github.com/nonie-sys/monero_extra_entropy/blob/master/hexdice.jpg "Hexadecimal dice")

## --extra-entropy

**monero-wallet-cli** supports the command line option `--extra-entropy`.

```bash
 --extra-entropy arg                    File containing extra entropy to 
                                        initialize the PRNG (any data, aim for 
                                        256 bits of entropy to be useful, wihch
                                        typically means more than 256 bits of 
                                        data)

```
*--extra-entropy* reads random data from a binary file - but first we need to roll some dice.

## Dice

The hexadecimal dice contains the following numbers: **0, 1, 2, 3, 4, 5, 6, 7, 8, 9, A, B, C, D, E, F**.
Two hexadecimal numbers require one byte of memory. e.G. You roll two F. (F,F) = 255 in decimal => 1 byte = 8 bit.
To create 256 bits of data (entropy) we need to roll the two dice at *least* 32 times. `256 bit/8 bit = 32`.
For one dice you need to roll double the amount - 64 times.

Make sure you buy two different colored dice or mark one dice with a dot. Always write down the number on the marked dice first. Your brain could be biased and always write down the smaller number first. We want to avoid such behavior.

## Create some entropy

The sequence after `echo` is where you write down the result of your dice. In this example i have rolled the dice 40 times to be on the safe side.

```bash
export HISTFILE=/dev/null
echo 50F38D07032E63933A5317D8FE3E0B4ED92560ABE310CC98BCFA54F3B196B2CC3FA219A494C49A7C | xxd -r -p > /dev/shm/entropy
```
xxd writes your hex sequence to a file. Try to avoid writing the file on a hard drive. `/dev/shm` is shared memory that can be used for this purpose. `export HISTFILE=/dev/null` makes sure that linux wont write the following command in the *.bash_history*.

## Create new wallet

At last we can create a new wallet.

```bash
./monero-wallet-cli --generate-new-wallet mywallet --extra-entropy /dev/shm/entropy
```
Protect your wallet with a phassphrase.

`--generate-new-wallet` creates a mnemonic seed of 25 words. Please write down those 25 words on a piece of paper and keep it safe. With the mnemonic seed you can restore your wallet if it gets lost.
