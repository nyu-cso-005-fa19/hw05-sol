## Problem 1

1. a. VPN: 5-12

   b. PTO: 0-4
   
   c. TLB set index: 4-6
   
   d. TLB tag: 7-12
   
2. a. Virtual address: 0x027C

      Binary: 000100 11 11100

      1. VPN: 0b00010011 = 0x13
      2. PTO: 0b11100 = 0x1C
      3. Idx: 0b11 = 0x3
      4. Tag: 0b000100 = 0x4
      5. TLB miss
      6. page fault
      7. PA: -
   
2. a. Virtual address: 0x03a9

      Binary: 000111 01 01001

      1. VPN: 0x1D
      2. PTO: 0x9
      3. Idx: 0x1
      4. Tag: 0x7
      5. TLB miss
      6. page fault
      7. PA: -

2. a. Virtual address: 0x0040

      Binary: 000000 10 00000

      1. VPN: 0x02
      2. PTO: 0x00
      3. Idx: 0x02
      4. Tag: 0x00
      5. TLB miss
      6. valid page
      7. PA: 0x33 << 5 + 0x00 = 0b11001100000 = 0x660

## Problem 2

1. a. Bits 0 and 1
   
   b. Bits 2-4
   
   c. Bits 5-12

1. a. Address 0x0FD1

   Binary: 01111110 100 01

   1. Block offset: 0x1
   
   2. Set index: 0x4
   
   3. Tag: 0x7E

   4. Cache hit
   
   5. Cache byte returned: 0x21

   
   b. Address 0x071A
   
   Binary: 00111000 110 10
   
   1. Block offset: 0x2
   
   2. Set index: 0x6
   
   3. Tag: 0x38

   4. Cache miss
   
   5. Cache byte returned: -

   c. Address 0x1407
   
   Binary: 10100000 001 11

   1. Block offset: 0x3
   
   2. Set index: 0x1
   
   3. Tag: A0
   
   4. Cache miss
   
   5. Cache byte returned: -

3. Tag: 0b10001010

   Set index: 0b111
   
   Block offset: 0x01
   
   Address: 0b10001010 111 01 = 0x115D

## Problem 3

First, note that since the total size of the cache is 4KB and every
cache line holds 16 bytes, there are 4096/16=256 entries in the cache.
The index at which a cache line is stored within the cache is
determined by its address. Since every cache line holds 16 bytes, the
least log(16)=4 bits of the address are used to encode the cache line
offset (i.e. the least significant digit of the address, written in
hex). The next log(256)=8 bits of the address are used to encode the
cache index (i.e. the second and third least significant digits of the
address, written in hex).

Second, note that because `sizeof(int)=4` and a cache line can hold 16
bytes, we know that exactly 4 array entries fit into one cache
line. The least three significant hex digits of the address 0x8000000
are 000, meaning that a[0][0] is the start of a cache line
a[0][0]..a[0][3] that will be stored at index 0x00 in the
cache. Address 0x8000010 ist the start of the cache line
a[0][4]..a[0][7] that will be stored at index 0x01 in the cache,
etc. In particular, the first byte a[1][0] of the second row of the
array is at address 0x8000100 and will be stored at cache index
0x10=16. The first byte a[16][0] of the 17th row is at address
0x8001000 and its cache line will again be stored at index 0x00, etc.

Now, when `sumA` executes, every 4-th array access will be a cache
miss and all others will be cache hits, yielding a cache miss ration
of 1/4.

On the other hand, when `sumB` executes, then every array access will
be a cache miss, yielding a cache miss ration of 1. This is because
the first 16 iterations of the inner loop for `j=0`, will access the
entries a[0][0], a[1][0], ... a[15][0], whose cache lines are all
different and will be stored at cache indices 0x00, 0x10=16, ..., 0xF0=240. At
`i=16`, the loop accesses a[16][0] whose cache line will again be
stored at index 0x00, replacing the cache line for a[0][0], at `i=17` it
accesses a[17][0] which will be stored again at index 0x10=16, replacing
the cache line for a[1][0], and so forth. By the time `i=32`, all the
cache lines loaded in the first 16 iterations have been replaced,
without a single cache hit. The whole process then just repeats.

Only when we can iterate over the inner loop completely without
replacing any cache lines will `sumB` have the same cache behavior as
`sumA`. Thus, let's determine the maximum `N` for which this is
possible. For this, we have to maximize `N` subject to

`N x N x 4 / 16 <= 256`

which yields `N=32`. Hence, for `N<=32`, the cache hit ratio for
`sumB` will also be 1/4. For, `N=33` it will be greater than
1/4. Hence, `N=33` is the smallest value of `N` for which `sumA` and
`sumB` have different cache miss ratios.

## Problem 4

* First block: block size 16 bytes, header: 0b10001 = 0x11
* Second block: block size 16 bytes, header: 0b10001 = 0x11
* Third block: block size 32 bytes, header: 0b100001 = 0x21
* Fourth block: block size 32 bytes, header: 0b100001 = 0x21

