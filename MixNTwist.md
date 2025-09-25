# MixNTwist

Category: BLOCKCHAIN

Points: 352

Solves: 20

>My friend sent me this contract, he said he mixed 5 contracts in it, how is that possible ? Just find what's hidden, it's driving me crazy

We are asked to download a file which contains `608060405234801561000f575f5ffd5b5060405180610b0001604052806...`

We can then put this bytecode into https://app.dedaub.com/decompile and have it decompiled for us to yield:

```js
function 0x328(uint256 varg0, bytes varg1) private { 
    require(varg1.length <= uint64.max, Panic(65)); // failed memory allocation (too much memory)
    v0 = STORAGE[varg0] >> 1;
    if (!(STORAGE[varg0] & 0x1)) {
        v0 = v1 = v0 & 0x7f;
    }
    require((STORAGE[varg0] & 0x1) - (v0 < 32), Panic(34)); // access to incorrectly encoded storage byte array
    if (v0 > 31) {
        v2 = v3 = keccak256(varg0);
        v2 = v4 = v3 + (varg1.length + 31 >> 5);
        while (v2 < v3 + (v0 + 31 >> 5)) {
            STORAGE[v2] = STORAGE[v2] & 0x0 | uint256(0);
            v2 = v2 + 1;
        }
    }
    v5 = v6 = 32;
    if (varg1.length > 31 == 1) {
        v7 = keccak256(varg0);
        v8 = v9 = 0;
        while (v8 < varg1.length & 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffe0) {
            STORAGE[v7] = MEM[varg1 + v5];
            v7 = v7 + 1;
            v5 = v5 + 32;
            v8 = v8 + 32;
        }
        if (varg1.length & 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffe0 < varg1.length) {
            STORAGE[v7] = MEM[varg1 + v5] & ~(uint256.max >> ((varg1.length & 0x1f) << 3));
        }
        STORAGE[varg0] = (varg1.length << 1) + 1;
        return ;
    } else {
        v10 = v11 = 0;
        if (varg1.length) {
            v10 = v12 = MEM[varg1.data];
        }
        STORAGE[varg0] = v10 & ~(uint256.max >> (varg1.length << 3)) | varg1.length << 1;
        return ;
    }
}

// Note: The function selector is not present in the original solidity code.
// However, we display it for the sake of completeness.

function __function_selector__() public nonPayable { 
    0x328(0, '0x608060405234801561000...');
    0x328(1, '0x6080604052601167fffff...');
    0x328(2, '0x6080604052600867fffff...');
    0x328(3, '0x6080604052606267fffff...');
    0x328(4, '0x60806040526040518060c...');
    MEM[0:1229] = 0x6080604052348015610...;
    return MEM[0:1229];
}
```

We can ignore most of the top code defining function `0x328` and just focus on the the 5 different contracts in function selector as hinted to by the flavor text. We will now examine each of these contracts seperately.

## Contract 0x328(0, '0x608060405234801561000...');

Decompiling the contract we get:

```js
function 0x296(uint256 varg0, bytes varg1) private { 
    require(varg1.length <= uint64.max, Panic(65)); // failed memory allocation (too much memory)
    v0 = STORAGE[varg0] >> 1;
    if (!(STORAGE[varg0] & 0x1)) {
        v0 = v1 = v0 & 0x7f;
    }
    require((STORAGE[varg0] & 0x1) - (v0 < 32), Panic(34)); // access to incorrectly encoded storage byte array
    ...
}

// Note: The function selector is not present in the original solidity code.
// However, we display it for the sake of completeness.

function __function_selector__() public nonPayable { 
    0x296(0, 0x637265777b);
    MEM[0:505] = 0x60806040523480156100...;
    return MEM[0:505];
}
```

While we could try to begin by reversing the function `0x296` which would be really difficult, instead we will just do some exploration and guessing. Notably the function call `0x296(0, 0x637265777b)` seems to be important. It turns out that converting `637265777b` from hex yields `crew{` and our first part of the flag

## Contract 0x328(1, '0x6080604052601167fffff...');

Decompiling the 2nd contract we get:

```js
// Data structures and variables inferred from the use of storage instructions
uint256[] ___function_selector__; // STORAGE[0x0]
uint256[] array_1; // STORAGE[0x1]

// Note: The function selector is not present in the original solidity code.
// However, we display it for the sake of completeness.

function __function_selector__() public payable { 
    require(17 <= uint64.max, Panic(65)); // failed memory allocation (too much memory)
    v0 = new uint256[](17);
    if (17) {
        CALLDATACOPY(v0.data, msg.data.length, 544);
    }
    v1 = v2 = v0.data;
    ___function_selector__.length = v0.length;
    v3 = v4 = ___function_selector__.data;
    if (v0.length) {
        while (v2 + 544 > v1) {
            STORAGE[v3] = MEM[v1];
            v1 += 32;
            v3 += 1;
        }
    }
    while (v4 + ___function_selector__.length > v3) {
        STORAGE[v3] = 0;
        v3 += 1;
    }
    require(10 <= uint64.max, Panic(65)); // failed memory allocation (too much memory)
    v5 = new uint256[](10);
    if (10) {
        CALLDATACOPY(v5.data, msg.data.length, 320);
    }
    v6 = v7 = v5.data;
    array_1.length = v5.length;
    v8 = v9 = array_1.data;
    if (v5.length) {
        while (v7 + 320 > v6) {
            STORAGE[v8] = MEM[v6];
            v6 += 32;
            v8 += 1;
        }
    }
    while (v9 + array_1.length > v8) {
        STORAGE[v8] = 0;
        v8 += 1;
    }
    require(!msg.value);
    v10 = v11 = new bytes[](uint8(116));
    MEM[v11.data] = uint8(104);
    v11[32][32] = uint8(105);
    MEM[64 + v11.data] = uint8(115);
    MEM[96 + v11.data] = uint8(105);
    MEM[128 + v11.data] = uint8(115);
    MEM[160 + v11.data] = uint8(116);
    MEM[192 + v11.data] = uint8(104);
    MEM[224 + v11.data] = uint8(101);
    MEM[256 + v11.data] = uint8(102);
    MEM[288 + v11.data] = uint8(105);
    MEM[320 + v11.data] = uint8(114);
    MEM[352 + v11.data] = uint8(115);
    MEM[384 + v11.data] = uint8(116);
    MEM[416 + v11.data] = uint8(109);
    MEM[448 + v11.data] = uint8(105);
    MEM[480 + v11.data] = uint8(120);
    ___function_selector__.length = 17;
    v12 = v13 = ___function_selector__.data;
    if (17) {
        while (v11 + 544 > v10) {
            STORAGE[v12] = uint8(MEM[v10]);
            v10 += 32;
            v12 += 1;
        }
    }
    while (v13 + ___function_selector__.length > v12) {
        STORAGE[v12] = 0;
        v12 += 1;
    }
    v14 = v15 = 22801;
    MEM[64 + v15.data] = uint8(66);
    MEM[96 + v15.data] = uint8(7);
    MEM[128 + v15.data] = uint8(52);
    MEM[160 + v15.data] = uint8(43);
    MEM[192 + v15.data] = uint8(91);
    MEM[224 + v15.data] = uint8(51);
    MEM[256 + v15.data] = uint8(43);
    array_1.length = 10;
    v16 = v17 = array_1.data;
    if (10) {
        while (v15 + 320 > v14) {
            STORAGE[v16] = uint8(MEM[v14]);
            v14 += 32;
            v16 += 1;
        }
    }
    while (v17 + array_1.length > v16) {
        STORAGE[v16] = 0;
        v16 += 1;
    }
    MEM[0:1674] = 0x608060405234801561000...;
    return MEM[0:1674];
}
```

Looking through the code we note 2 interesting parts, at address v10/v11 we are storing an array of length 17, and at address v14/v15 we are storing an array of length 10.

For the first array we can fairly easily note down all 17 bytes:

```js
v10 = v11 = new bytes[](uint8(116));
MEM[v11.data] = uint8(104);
v11[32][32] = uint8(105);
MEM[64 + v11.data] = uint8(115);
MEM[96 + v11.data] = uint8(105);
MEM[128 + v11.data] = uint8(115);
MEM[160 + v11.data] = uint8(116);
MEM[192 + v11.data] = uint8(104);
MEM[224 + v11.data] = uint8(101);
MEM[256 + v11.data] = uint8(102);
MEM[288 + v11.data] = uint8(105);
MEM[320 + v11.data] = uint8(114);
MEM[352 + v11.data] = uint8(115);
MEM[384 + v11.data] = uint8(116);
MEM[416 + v11.data] = uint8(109);
MEM[448 + v11.data] = uint8(105);
MEM[480 + v11.data] = uint8(120);
```

so we get:
`116 104 105 115 105 115 116 104 101 102 105 114 115 116 109 105 120` equals `thisisthefirstmix`

Unfortunately we are not done since the 2nd array will also turn out to be important.

We then take a look at the 2nd array which is supposed to contain 10 values. While 7 bytes are obvious, the other 3 aren't as easy to see. 

```js
v14 = v15 = `22801`;
MEM[64 + v15.data] = uint8(66);
MEM[96 + v15.data] = uint8(7);
MEM[128 + v15.data] = uint8(52);
MEM[160 + v15.data] = uint8(43);
MEM[192 + v15.data] = uint8(91);
MEM[224 + v15.data] = uint8(51);
MEM[256 + v15.data] = uint8(43);
```

First the decompiler treats `22801` as a number when it would've been more useful to rewrite it in hex as `0x5911`. So now we are just missing one byte. Unfortunately, this is where the decompiler isn't perfect so we must dig deeper into the disassembled code to find the mistake. (This took so long to figure out)

![Missing Byte](/images/Contract2MissingByte.png)

Ok we now have all the values of the 2nd array but now what? We must further dig into the bytecode on line 106 in the image above. Decompiling that, we get:

```js
// Data structures and variables inferred from the use of storage instructions
uint256[] _validate; // STORAGE[0x0]
uint256[] array_1; // STORAGE[0x1]

function fallback() public payable { 
    revert();
}

function validate(uint256[] _frenzyDucks) public payable { 
    require(4 + (msg.data.length - 4) - 4 >= 32);
    require(_frenzyDucks <= uint64.max);
    require(4 + _frenzyDucks + 31 < 4 + (msg.data.length - 4));
    v0 = v1 = _frenzyDucks.data;
    require(_frenzyDucks.length <= uint64.max, Panic(65)); // failed memory allocation (too much memory)
    v2 = new uint256[](_frenzyDucks.length);
    require(!((v2 + ((_frenzyDucks.length << 5) + 32 + 31 & 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffe0) > uint64.max) | (v2 + ((_frenzyDucks.length << 5) + 32 + 31 & 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffe0) < v2)), Panic(65)); // failed memory allocation (too much memory)
    v3 = v4 = v2.data;
    require(v1 + (_frenzyDucks.length << 5) <= 4 + (msg.data.length - 4));
    while (v0 < v1 + (_frenzyDucks.length << 5)) {
        require(msg.data[v0] == msg.data[v0]);
        MEM[v3] = msg.data[v0];
        v3 = v3 + 32;
        v0 = v0 + 32;
    }
    v5 = new uint256[](_validate.length);
    v6 = v7 = v5.data;
    if (_validate.length) {
        v8 = v9 = _validate.data;
        do {
            MEM[v6] = STORAGE[v8];
            v6 += 32;
            v8 += 1;
        } while (v7 + (_validate.length << 5) > v6);
    }
    require(v5.length > 0, Error('empty key'));
    require(v2.length <= uint64.max, Panic(65)); // failed memory allocation (too much memory)
    v10 = new bytes[](v2.length);
    if (v2.length) {
        CALLDATACOPY(v10.data, msg.data.length, v2.length);
    }
    v11 = v12 = 0;
    while (v11 < v2.length) {
        require(v5.length, Panic(18)); // division by zero
        require(v11 % v5.length < v5.length, Panic(50)); // access an out-of-bounds or negative index of bytesN array or slice
        require(v11 < v2.length, Panic(50)); // access an out-of-bounds or negative index of bytesN array or slice
        require(v11 < v10.length, Panic(50)); // access an out-of-bounds or negative index of bytesN array or slice
        MEM8[32 + v11 + v10] = (byte(bytes1((v2[v11] ^ v5[v11 % v5.length]) << 248), 0x0)) & 0xFF;
        v11 += 1;
    }
    v13 = new uint256[](v14 - MEM[64] - 32);
    v14 = v15 = v13.data;
    v16 = v17 = array_1.data;
    v18 = v19 = 0;
    while (v18 < array_1.length) {
        MEM[v14] = STORAGE[v16];
        v14 = v14 + 32;
        v16 = v16 + 1;
        v18 = v18 + 1;
    }
    MEM[64] = v14;
    v20 = v13.length;
    v21 = v13.data;
    v22 = v10.length;
    v23 = v10.data;
    return keccak256(v10) == keccak256(v13);
}

// Note: The function selector is not present in the original solidity code.
// However, we display it for the sake of completeness.

function __function_selector__( function_selector) public payable { 
    MEM[64] = 128;
    require(!msg.value);
    if (msg.data.length >= 4) {
        if (0x25b90494 == function_selector >> 224) {
            validate(uint256[]);
        }
    }
    fallback();
}
```
While this seems complicated at first glace, we just need to focus on the lines:
```js
v11 = v12 = 0;
while (v11 < v2.length) {
    require(v5.length, Panic(18)); // division by zero
    require(v11 % v5.length < v5.length, Panic(50)); // access an out-of-bounds or negative index of bytesN array or slice
    require(v11 < v2.length, Panic(50)); // access an out-of-bounds or negative index of bytesN array or slice
    require(v11 < v10.length, Panic(50)); // access an out-of-bounds or negative index of bytesN array or slice
    MEM8[32 + v11 + v10] = (byte(bytes1((v2[v11] ^ v5[v11 % v5.length]) << 248), 0x0)) & 0xFF;
    v11 += 1;
}
```
We can note that this is just a simple xor cipher, so if we then just xor our first array with the second array and take the first 10 bytes, we get the 2nd part of the flag:

![XOR](/images/Contract2XORED.png)

`M1x1nG_3VM`

## Contract 0x328(2, '0x6080604052600867fffff...');

Decompiling the 3rd contract we get

```js
// Data structures and variables inferred from the use of storage instructions
uint256[] ___function_selector__; // STORAGE[0x0]



// Note: The function selector is not present in the original solidity code.
// However, we display it for the sake of completeness.

function __function_selector__() public payable { 
    require(8 <= uint64.max, Panic(65)); // failed memory allocation (too much memory)
    v0 = new uint256[](8);
    if (8) {
        CALLDATACOPY(v0.data, msg.data.length, uint8.max + 1);
    }
    v1 = v2 = v0.data;
    ___function_selector__.length = v0.length;
    v3 = v4 = ___function_selector__.data;
    if (v0.length) {
        while (v2 + (uint8.max + 1) > v1) {
            STORAGE[v3] = MEM[v1];
            v1 += 32;
            v3 += 1;
        }
    }
    while (v4 + ___function_selector__.length > v3) {
        STORAGE[v3] = 0;
        v3 += 1;
    }
    require(!msg.value);
    v5 = v6 = 0x5f736c;
    MEM[96 + v6.data] = uint8(49);
    MEM[128 + v6.data] = uint8(49);
    MEM[160 + v6.data] = uint8(51);
    MEM[192 + v6.data] = uint8(95);
    ___function_selector__.length = 8;
    v7 = v8 = ___function_selector__.data;
    if (8) {
        while (v6 + (uint8.max + 1) > v5) {
            STORAGE[v7] = uint8(MEM[v5]);
            v5 += 32;
            v7 += 1;
        }
    }
    while (v8 + ___function_selector__.length > v7) {
        STORAGE[v7] = 0;
        v7 += 1;
    }
    MEM[0:1368] = 0x6080604052348015610...3;
    return MEM[0:1368];
}
```

Looking at this code we note that we are creating an array of length 8 this time over here:

```js
v5 = v6 = 0x5f736c;
MEM[96 + v6.data] = uint8(49);
MEM[128 + v6.data] = uint8(49);
MEM[160 + v6.data] = uint8(51);
MEM[192 + v6.data] = uint8(95);
___function_selector__.length = 8;
```

Unfortunately, just like in the 2nd contract, 1 byte is missing, as we see 4 individual bytes and `0x5f736c` is 3 bytes.

![Missing Byte in Contract 3](/images/Contract3MissingByte.png)

ok now that we have all the bytes `0x4b 0x5f 0x73 0x6c 49 49 51 95` we can decode them into `K_sl113_`. However, we still aren't finished. So just like contract 2, we need to find out what the byte code on line 53 above is doing:

```js
function fallback() public payable { 
    revert();
}

function 0xe3307d39(uint256[] varg0) public payable { 
    require(4 + (msg.data.length - 4) - 4 >= 32);
    require(varg0 <= uint64.max);
    require(4 + varg0 + 31 < 4 + (msg.data.length - 4));
    v0 = v1 = varg0.data;
    require(varg0.length <= uint64.max, Panic(65)); // failed memory allocation (too much memory)
    v2 = new uint256[](varg0.length);
    require(!((v2 + ((varg0.length << 5) + 32 + 31 & 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffe0) > uint64.max) | (v2 + ((varg0.length << 5) + 32 + 31 & 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffe0) < v2)), Panic(65)); // failed memory allocation (too much memory)
    v3 = v4 = v2.data;
    require(v1 + (varg0.length << 5) <= 4 + (msg.data.length - 4));
    while (v0 < v1 + (varg0.length << 5)) {
        require(msg.data[v0] == msg.data[v0]);
        MEM[v3] = msg.data[v0];
        v3 = v3 + 32;
        v0 = v0 + 32;
    }
    v5 = 0x5d(v2);
    v6 = new uint256[](v5.length);
    v7 = v8 = v6.data;
    v9 = v10 = v5.data;
    v11 = v12 = 0;
    while (v11 < v5.length) {
        MEM[v7] = MEM[v9];
        v7 = v7 + 32;
        v9 = v9 + 32;
        v11 = v11 + 1;
    }
    return v6;
}

function 0x5d(uint256 varg0) private { 
    require(varg0.length == 8, Error('Bad length'));
    v0 = new uint256[](uint8(7));
    MEM[v0.data] = uint8(5);
    MEM[32 + v0.data] = uint8(2);
    MEM[64 + v0.data] = uint8(1);
    MEM[96 + v0.data] = uint8(3);
    MEM[128 + v0.data] = uint8(4);
    MEM[160 + v0.data] = uint8(0);
    MEM[192 + v0.data] = uint8(6);
    require(varg0.length <= uint64.max, Panic(65)); // failed memory allocation (too much memory)
    v1 = new uint256[](varg0.length);
    if (varg0.length) {
        CALLDATACOPY(v1.data, msg.data.length, varg0.length << 5);
    }
    v2 = v3 = 0;
    while (v2 < varg0.length) {
        require(v2 < 8, Panic(50)); // access an out-of-bounds or negative index of bytesN array or slice
        require(uint8(MEM[(v2 << 5) + v0]) < varg0.length, Panic(50)); // access an out-of-bounds or negative index of bytesN array or slice
        require(v2 < v1.length, Panic(50)); // access an out-of-bounds or negative index of bytesN array or slice
        v1[v2] = varg0[uint8(MEM[(v2 << 5) + v0])];
        v2 += 1;
    }
    return v1;
}

// Note: The function selector is not present in the original solidity code.
// However, we display it for the sake of completeness.

function __function_selector__( function_selector) public payable { 
    MEM[64] = 128;
    require(!msg.value);
    if (msg.data.length >= 4) {
        if (0xe3307d39 == function_selector >> 224) {
            0xe3307d39();
        }
    }
    fallback();
}
```

Looking through the code we see a function call to `0xe3307d39`. Inside `0xe3307d39` there are a bunch of checks and then it calls function `0x5d`. Inside `0x5d` we see the creation of a new 8 length array `v0 = [7,5,2,1,3,4,0,6]` and an interesting loop:

```js
while (v2 < varg0.length) {
    require(v2 < 8, Panic(50)); // access an out-of-bounds or negative index of bytesN array or slice
    require(uint8(MEM[(v2 << 5) + v0]) < varg0.length, Panic(50)); // access an out-of-bounds or negative index of bytesN array or slice
    require(v2 < v1.length, Panic(50)); // access an out-of-bounds or negative index of bytesN array or slice
    v1[v2] = varg0[uint8(MEM[(v2 << 5) + v0])];
    v2 += 1;
}
```

Ignoring all the checks, we see the main logic `v1[v2] = varg0[uint8(MEM[(v2 << 5) + v0])];`

Essentially we are setting the ith index of v1 to be the v0[i]th value in our input array `K_sl113_`, a permuation cipher. So we get:

```

7 -> _
5 -> 1
2 -> s
1 -> _
3 -> l
4 -> 1
0 -> K
6 -> 3
```

Thus the 3rd part of our flag is `_1s_l1K3`.

## Contract 0x328(3, '0x6080604052606267fffff...');

Decomiling the 4th contract we get;

```js
// Data structures and variables inferred from the use of storage instructions
uint256[] ___function_selector__; // STORAGE[0x0]

function 0x79a(uint256 varg0, bytes varg1) private { 
    require(varg1.length <= uint64.max, Panic(65)); // failed memory allocation (too much memory)
    v0 = STORAGE[varg0] >> 1;
    if (!(STORAGE[varg0] & 0x1)) {
        v0 = v1 = v0 & 0x7f;
    }
    require((STORAGE[varg0] & 0x1) - (v0 < 32), Panic(34)); // access to incorrectly encoded storage byte array
    if (v0 > 31) {
        v2 = v3 = keccak256(varg0);
        v2 = v4 = v3 + (varg1.length + 31 >> 5);
        while (v2 < v3 + (v0 + 31 >> 5)) {
            STORAGE[v2] = STORAGE[v2] & 0x0 | uint256(0);
            v2 = v2 + 1;
        }
    }
    v5 = v6 = 32;
    if (varg1.length > 31 == 1) {
        v7 = keccak256(varg0);
        v8 = v9 = 0;
        while (v8 < varg1.length & 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffe0) {
            STORAGE[v7] = MEM[varg1 + v5];
            v7 = v7 + 1;
            v5 = v5 + 32;
            v8 = v8 + 32;
        }
        if (varg1.length & 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffe0 < varg1.length) {
            STORAGE[v7] = MEM[varg1 + v5] & ~(uint256.max >> ((varg1.length & 0x1f) << 3));
        }
        STORAGE[varg0] = (varg1.length << 1) + 1;
        return ;
    } else {
        v10 = v11 = 0;
        if (varg1.length) {
            v10 = v12 = MEM[varg1.data];
        }
        STORAGE[varg0] = v10 & ~(uint256.max >> (varg1.length << 3)) | varg1.length << 1;
        return ;
    }
}

// Note: The function selector is not present in the original solidity code.
// However, we display it for the sake of completeness.

function __function_selector__() public payable { 
    require(98 <= uint64.max, Panic(65)); // failed memory allocation (too much memory)
    v0 = new uint256[](98);
    if (98) {
        CALLDATACOPY(v0.data, msg.data.length, 3136);
    }
    v1 = v2 = v0.data;
    ___function_selector__.length = v0.length;
    v3 = v4 = ___function_selector__.data;
    if (v0.length) {
        while (v2 + 3136 > v1) {
            STORAGE[v3] = MEM[v1];
            v1 += 32;
            v3 += 1;
        }
    }
    while (v4 + ___function_selector__.length > v3) {
        STORAGE[v3] = 0;
        v3 += 1;
    }
    0x79a(1, 'What is this URL for ?');
    require(!msg.value);
    v5 = v6 = new bytes[](uint8(104));
    MEM[v6.data] = uint8(116);
    v6[32][32] = uint8(116);
    MEM[64 + v6.data] = uint8(112);
    MEM[96 + v6.data] = uint8(115);
    MEM[128 + v6.data] = uint8(58);
    MEM[160 + v6.data] = uint8(47);
    MEM[192 + v6.data] = uint8(47);
    MEM[224 + v6.data] = uint8(115);
    MEM[256 + v6.data] = uint8(101);
    MEM[288 + v6.data] = uint8(112);
    MEM[320 + v6.data] = uint8(111);
    MEM[352 + v6.data] = uint8(108);
    MEM[384 + v6.data] = uint8(105);
    MEM[416 + v6.data] = uint8(97);
    MEM[448 + v6.data] = uint8(46);
    MEM[480 + v6.data] = uint8(101);
    MEM[512 + v6.data] = uint8(116);
    MEM[544 + v6.data] = uint8(104);
    MEM[576 + v6.data] = uint8(101);
    MEM[608 + v6.data] = uint8(114);
    MEM[640 + v6.data] = uint8(115);
    MEM[672 + v6.data] = uint8(99);
    MEM[704 + v6.data] = uint8(97);
    MEM[736 + v6.data] = uint8(110);
    MEM[768 + v6.data] = uint8(46);
    MEM[800 + v6.data] = uint8(105);
    MEM[832 + v6.data] = uint8(111);
    MEM[864 + v6.data] = uint8(47);
    MEM[896 + v6.data] = uint8(116);
    v7 = 928 + v6.data;
    MEM[v7] = uint8(120);
    MEM[32 + v7] = uint8(47);
    MEM[64 + v7] = uint8(48);
    MEM[96 + v7] = uint8(120);
    MEM[128 + v7] = uint8(48);
    MEM[160 + v7] = uint8(52);
    MEM[192 + v7] = uint8(52);
    MEM[224 + v7] = uint8(57);
    MEM[256 + v7] = uint8(57);
    MEM[288 + v7] = uint8(48);
    MEM[320 + v7] = uint8(56);
    MEM[352 + v7] = uint8(98);
    MEM[384 + v7] = uint8(57);
    MEM[416 + v7] = uint8(54);
    MEM[448 + v7] = uint8(100);
    MEM[480 + v7] = uint8(101);
    MEM[512 + v7] = uint8(50);
    MEM[544 + v7] = uint8(100);
    MEM[576 + v7] = uint8(54);
    MEM[608 + v7] = uint8(98);
    MEM[640 + v7] = uint8(56);
    MEM[672 + v7] = uint8(102);
    MEM[704 + v7] = uint8(54);
    MEM[736 + v7] = uint8(56);
    MEM[768 + v7] = uint8(52);
    MEM[800 + v7] = uint8(55);
    MEM[832 + v7] = uint8(102);
    MEM[864 + v7] = uint8(97);
    MEM[896 + v7] = uint8(54);
    v8 = 928 + v7;
    MEM[v8] = uint8(56);
    MEM[32 + v8] = uint8(97);
    MEM[64 + v8] = uint8(98);
    MEM[96 + v8] = uint8(49);
    MEM[128 + v8] = uint8(51);
    MEM[160 + v8] = uint8(53);
    MEM[192 + v8] = uint8(54);
    MEM[224 + v8] = uint8(99);
    MEM[256 + v8] = uint8(50);
    MEM[288 + v8] = uint8(48);
    MEM[320 + v8] = uint8(54);
    MEM[352 + v8] = uint8(98);
    MEM[384 + v8] = uint8(55);
    MEM[416 + v8] = uint8(48);
    MEM[448 + v8] = uint8(48);
    MEM[480 + v8] = uint8(56);
    MEM[512 + v8] = uint8(50);
    MEM[544 + v8] = uint8(53);
    MEM[576 + v8] = uint8(56);
    MEM[608 + v8] = uint8(98);
    MEM[640 + v8] = uint8(97);
    MEM[672 + v8] = uint8(50);
    MEM[704 + v8] = uint8(49);
    MEM[736 + v8] = uint8(99);
    MEM[768 + v8] = uint8(102);
    MEM[800 + v8] = uint8(56);
    MEM[832 + v8] = uint8(49);
    MEM[864 + v8] = uint8(51);
    MEM[896 + v8] = uint8(99);
    v9 = 928 + v8;
    MEM[v9] = uint8(51);
    MEM[32 + v9] = uint8(54);
    MEM[64 + v9] = uint8(56);
    MEM[96 + v9] = uint8(101);
    MEM[128 + v9] = uint8(52);
    MEM[160 + v9] = uint8(100);
    MEM[192 + v9] = uint8(48);
    MEM[224 + v9] = uint8(99);
    MEM[256 + v9] = uint8(49);
    MEM[288 + v9] = uint8(48);
    ___function_selector__.length = 98;
    v10 = v11 = ___function_selector__.data;
    if (98) {
        while (v6 + 3136 > v5) {
            STORAGE[v10] = uint8(MEM[v5]);
            v5 += 32;
            v10 += 1;
        }
    }
    while (v11 + ___function_selector__.length > v10) {
        STORAGE[v10] = 0;
        v10 += 1;
    }
    MEM[0:506] = 0x6080604052348015610...;
    return MEM[0:506];
}
```

The most notable part of this snippet is:

```js
0x79a(1, 'What is this URL for ?');
require(!msg.value);
v5 = v6 = new bytes[](uint8(104));
MEM[v6.data] = uint8(116);
v6[32][32] = uint8(116);
...
```

Using some regex, we can then get the url that the array stores:

![CyberChef Formating the Array](/images/Contract4CyberChef.png)

Going to the url, we get the 4th part of the flag:

![Viewing the Url](/images/Contract4FlagPart.png)

`_TwwwIst1nGGG_`


## Contract 0x328(4, '0x60806040526040518060c...');

Decompiling the final contract we get:

```js
function 0x279(uint256 varg0, bytes varg1) private { 
    require(varg1.length <= uint64.max, Panic(65)); // failed memory allocation (too much memory)
    v0 = STORAGE[varg0] >> 1;
    if (!(STORAGE[varg0] & 0x1)) {
        v0 = v1 = v0 & 0x7f;
    }
    require((STORAGE[varg0] & 0x1) - (v0 < 32), Panic(34)); // access to incorrectly encoded storage byte array
    ...
}

// Note: The function selector is not present in the original solidity code.
// However, we display it for the sake of completeness.

function __function_selector__() public payable { 
    MEM[64] = 128;
    0x279(0, "I know you want that flag so i'm not going to make this last part hard : WHY zero you ARE underscore h three four D D closebracket");
    require(!msg.value);
    MEM[0:505] = 0x6080604052348015610...;
    return MEM[0:505];
}
```

Thankfully, we can just ignore all the code as we are given the final part `WHY zero you ARE underscore h three four D D closebracket` = `Y0uR_h34DD}`


## Flag

Finally, by combing all five parts of the flags together we get

`crew{M1x1nG_3VM_1s_l1K3_TwwwIst1nGGG_Y0uR_h34DD}`