---
sidebar_position: 1
---

# Bit mapping

---


## Binary bit encoding
To minimise storage costs (we should always do this where we can) we back felts with binary numbers.

For the Realms Data we are storing all the traits, resources and wonders within a single felt.

This technique was borrowed from the Dopewars engine (credit goes to @eth_worm)

### Step 1:  Define the values in binary

```
struct RealmData:
    member cities : felt  # eg: 7 cities = 111
    member regions : felt  # eg: 4 regions = 100
    member rivers : felt  # eg: 60 rivers = 111100
    member harbours : felt  #  eg: 10 harbours = 1010
    member resource_number : felt  #  eg: 5 resource_number = 101
    member resource_1 : felt  # eg: 1 resource_1 = 1
    member resource_2 : felt  # eg: 2 resource_2 = 10
    member resource_3 : felt  # eg: 3 resource_3 = 11
    member resource_4 : felt  # eg: 4 resource_4 = 100
    member resource_5 : felt  # eg: 5 resource_5 = 101
    member resource_6 : felt  # eg: 0 resource_6 = 0 (0 if no resource)
    member resource_7 : felt  # eg: 0 resource_7 = 0 (0 if no resource)
    member wonder : felt  # eg: 50 wonder = 110010 (50 wonders)
    member order : felt # eg: 3 = 11
end
```

### Step 2:  Pack binary bits

Define how large the mask is needed for a value.

We will use rivers as an example since it's highest value is 60, which equates to 6 bits. We will use an 8 bit mask on all values to keep things consistent (this could be what ever you like).

Next, take the binary values and create their 8 bit representations, e.g.:

| trait           | decimal | binary | 8 bit      |
| --------------- | ------- | ------ | ---------- |
| cities          | 7       | 111    | `00000111` |
| regions         | 4       | 100    | `00000100` |
| rivers          | 60      | 111100 | `00111100` |
| harbours        | 10      | 1010   | `00001010` |
| resource_number | 5       | 101    | `00000101` |
| resource_1      | 1       | 1      | `00000001` |
| resource_2      | 2       | 10     | `00000010` |
| resource_3      | 3       | 11     | `00000011` |
| resource_4      | 4       | 100    | `00000100` |
| resource_5      | 5       | 101    | `00000101` |
| resource_6      | 0       | 0      | `00000000` |
| resource_7      | 0       | 0      | `00000000` |
| wonder          | 50      | 110010 | `00110010` |
| order           | 3       | 10     | `00000011` |

Then concatenate the 8 bit values. This way, you'll get a 112 bit number (14 values \* 8 bits for each value). The value for cities (`00000111`) will be the least significant ("rightmost") and the value for order (`00000011`) will be the most significant ("leftmost") position:

```
0000001100110010000000000000000000000101000001000000001100000010000000010000010100001010001111000000010000000111
```

Then convert to decimal and this is the realms traits to store in the felt:

```
64808636960354064279015241024519
```

Then this function will unpack the the decimal into bits

```
unpack_data()
```

Same method is used for packing the values of resources needed to build

```
# ids - 8 bit
resource_1 = 5 = 00000001
resource_2 = 10 = 00000010
resource_3 = 12 = 00000011
resource_4 = 21 = 00000100
resource_5 = 9 = 00000101

0000010100000100000000110000001000000001

21542142465

# values 14 bit - max 10000 = 0b10011100010000
resource_1_values = 00000000001010
resource_2_values = 00000000001010
resource_3_values = 00000000001010
resource_4_values = 00000000001010
resource_5_values = 00000000001010

0000000000101000000000001010000000000010100000000000101000000000001010

720619923528908810
```