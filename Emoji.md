# Description:

Author: noob-abhinav

Emojis everywhere! Is it a joke? Or something is hiding behind it.

Text: 🁳🁣🁲🁩🁰🁴🁃🁔🁆🁻🀳🁭🀰🁪🀱🁟🀳🁮🁣🀰🁤🀱🁮🁧🁟🀱🁳🁟🁷🀳🀱🁲🁤🁟🀴🁮🁤🁟🁦🁵🁮🀡🀱🁥🀴🀶🁤🁽

#misc
# Solution:
Let us make a few observations:
1. Not all tiles are unique, some of them repeat, especially `🁟` 
2. 🁻 and 🁽 are suspiciously, only 2 units apart in Unicode. Does this ring a bell? Yes! Its the same difference as `{` and `}` (IDK why they are not adjacent, it just does not make sense)
Now, Let us check if the character codes of the characters.
For this we make a simple code:
```Python title="check.py"
text = "🁳🁣🁲🁩🁰🁴🁃🁔🁆🁻🀳🁭🀰🁪🀱🁟🀳🁮🁣🀰🁤🀱🁮🁧🁟🀱🁳🁟🁷🀳🀱🁲🁤🁟🀴🁮🁤🁟🁦🁵🁮🀡🀱🁥🀴🀶🁤🁽"

for i in text:
    print(ord(i))

```

```shell title=output
$ python check.py
127091
127075
127090
127081
127088
127092
127043
127060
127046
127099
...
```

If we observe,  
$$127091 - 127075 = 16$$also, 
$$c = 3$$
$$ s = 19$$
$$s - c = 16$$
Since we know that our flag starts with `scriptCTF{`, let us map `127091` to `s` and use that offset to calculate the flag.

# Python Code:
```Python title=solve.py
text = "🁳🁣🁲🁩🁰🁴🁃🁔🁆🁻🀳🁭🀰🁪🀱🁟🀳🁮🁣🀰🁤🀱🁮🁧🁟🀱🁳🁟🁷🀳🀱🁲🁤🁟🀴🁮🁤🁟🁦🁵🁮🀡🀱🁥🀴🀶🁤🁽"

nums = []

for i in text:
    nums.append(ord(i))

offset = nums[0] - ord("s")
decoded = "".join(chr(n - offset) for n in nums)

print("Decoded string:", decoded)
```
```shell title=output
Decoded string: scriptCTF{3m0j1_3nc0d1ng_1s_w31rd_4nd_fun!1e46d}
```