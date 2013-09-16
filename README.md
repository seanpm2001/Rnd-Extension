# NetLogo Rnd Extension

This extension adds the [`rnd:weighted-one-of`](#rndweighted-one-of--agentset-reporter-task), [`rnd:weighted-n-of`](#rndweighted-n-of-size-agentset-reporter-task) and [rnd:weighted-n-of-with-repeats](#rndweighted-n-of-with-repeats-size-agentset-reporter-task) primitives to NetLogo.

You can [download the Rnd extension from here](https://github.com/NetLogo/Rnd-Extension/releases/download/v1.1.0/rnd.zip). Just unzip the file under your NetLogo's extensions folder.

## Usage

#### `rnd:weighted-one-of`  _agentset_ _reporter-task_
#### `rnd:weighted-one-of`  _list_ _reporter-task_

From an agentset, reports a random agent. If the agentset is empty, reports [`nobody`](http://ccl.northwestern.edu/netlogo/docs/dictionary.html#nobody).

From a list, reports a random list item. It is an error for the list to be empty.

In both cases, the probability of each item being picked is proportional to the weight reported by _reporter-task_ for this item.

The weights reported by _reporter-task_ must not be negative.

If all weights are `0.0`, each candidate has an equal probability of being picked.

#### `rnd:weighted-n-of` _size_ _agentset_ _reporter-task_
#### `rnd:weighted-n-of` _size_ _list_ _reporter-task_

From an agentset, reports an agentset of size _size_ randomly chosen from the input set, with no repeats.

From a list, reports a list of size _size_ randomly chosen from the input set, with **no repeats**. The items in the result appear in the same order that they appeared in the input list. (If you want them in random order, use shuffle on the result.)

In both cases, the probability of each item being picked is proportional to the weight reported by _reporter-task_ for this item.

It is an error for _size_ to be greater than the size of the input.

The weights reported by _reporter-task_ must not be negative.

If, at some point during the selection, there remains only candidates with a weight of `0.0`, they all have an equal probability of getting picked.

#### `rnd:weighted-n-of-with-repeats` _size_ _agentset_ _reporter-task_
#### `rnd:weighted-n-of-with-repeats` _size_ _list_ _reporter-task_

From either an agent or a list, reports a **list** of size _size_ randomly chosen from the input set, **with repeats**. The items in the result appear in the same order that they appeared in the input list. (If you want them in random order, use shuffle on the result.)

The probability of each item being picked is proportional to the weight reported by _reporter-task_ for this item.

It is **not** an error for _size_ to be greater than the size of the input.

The weights reported by _reporter-task_ must not be negative.

If all weights are `0.0`, each candidate has an equal probability of being picked.

### A note about performance

The extension uses Keith Schwarz's implementation of Vose's Alias Method (see Schwarz's [Darts, Dice, and Coins](http://www.keithschwarz.com/darts-dice-coins/) page). Assuming you are choosing _n_ candidates for a collection of size _m_ **with repeats**, this method has an initialization cost of _O(m)_ followed by a cost of _O(1)_ for each item you pick, so _O(m + n)_ overall.

For example, in the following code:

    let candidates n-values 500 [ ? ]
    rnd:weighted-n-of-with-repeats 100 candidates [ ? ]
    n-values 100 [ rnd:weighted-one-of candidates [ ? ] ]

...the line using `rnd:weighted-n-of-with-repeats` will likely run 100 times faster than the line using a combination of `n-values` and `rnd:weighted-one-of`. This is because `rnd:weighted-n-of-with-repeats` only initializes the algorithm once and `rnd:weighted-one-of` does it each time it is called.

(Also note that composing `n-values` with `rnd:weighted-one-of` does not preserve the order of the original candidate list, while `rnd:weighted-n-of-with-repeats` does.)

Things are a bit more complicated if you are choosing **without repeats**, however. In this case, the algorithm may have to discard some picks because the candidates have already been selected. When this starts happening too often (maybe because some weights are much bigger than others), the extension re-initializes the algorithm with the already-picked candidates excluded. This should not happen too often, however, so while picking without repeats has an upper bound of _O(m * n)_ _in theory_, it should usually not be much more than O(m + n) in practice.

## Building

Run `./sbt package` to build the extension.

If the build succeeds, `rnd.jar` will be created.

## Credits

Authored by Nicolas Payette.

## Terms of Use

[![CC0](http://i.creativecommons.org/p/zero/1.0/88x31.png)](http://creativecommons.org/publicdomain/zero/1.0/)

The NetLogo Rnd extension is in the public domain. To the extent possible under law, Uri Wilensky has waived all copyright and related or neighboring rights.
