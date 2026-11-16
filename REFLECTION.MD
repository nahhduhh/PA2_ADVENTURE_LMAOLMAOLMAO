1..
For this, n is the number of scenes in the tree or in the subtree. Here, the scene text is seen as data that's fixed
Each scene has at most 3 children, what you input, inventory, and how the path-string gets copied.

Todo1 - Constructor. We give numChoices_& slots for scenes that don't end ans initialize each slot to nullptr. The ending doesn't need an array. If you wanted to initialize k slots, it costs
O(k), and with k being at most 3, the complexity is O(1) if you were seeing it as n.

Todo2- Destructor. We delete each child scene (ominous, i know), then the choices_ array with delete[], and then data_. The child's destructor
deletes the descendants, so getting rid of a full subtree of n scenes would take O(n).

Todo3- isEnding. We return if numChoices is 0. This takes O(1) time.

Todo4- getChoice. We check the bounds of the index and return nullptr or the pointer that's in the slot already. This also takes O(1).

Todo5- setchoice. We check also check the bounds of the index and store the pointer that's given, if the index is correct. This also takes O(1) time. It also doesn't make or copy a scene.

Todo6- countScenes. A nullpointer doesn't contrubute anything. Instead, we count the current scene as 1 and add the children's counts recursively. Each scene gets seen once, so this is O(n).

Todo7- countEndings. A nullpointer also doesn't contribute anything, a leaf contributes 1. Other scenes return the sum of the counts of the children. This takes O(n).

Todo8- height. Null pointer returns -1 here. In a real scene, we find the greatest height of the child and add one. Endings skip the loop and
returns 0, since the tallest starts at -1. This also takes O(n).

Todo9- clear. We delete root_ and set it to nullptr. Even though there are only 2 statements, deleting the root makes sure that the descendants get cleaned up, and this takes
O(n) time. If the tree is empty, it takes O(1).

Todo10- load. We load the file, read the starting hp, and readscene gets called with a root that's labeled START. If it works, we gives the new root to tree_, and if it doesn't,
we return false. Loading is O(n) time for scene based analysis, and similar to the size of the input when the text length gets counted. If a tree
existed beforehand, calling setRoot deletes it.

Todo11- readScene. We read the 5 fields, don't accept a choice count that's invalid, and recursively read each child that's labeled. If it doesn't work, deleting
a part of the scene cleans up its children, and it takes O(n) time.

Todo12- askChoice. We read a whole line and compare it to the valid choice numbers. If it matches, we return that number. Otherwise, we ask again. 
If the input ends, we return 0. Since there are at most 3 choices, checking one fixed-length input takes O(1) time. The whole function doesn't have a set limit based on n, 
because someone could keep typing the wrong thing forever. Reading a longer input also takes more time.

Todo13- play. We follow the player's choices through the story. Every time we enter a scene, we update HP and loot, check for an item, and print what happens. 
We stop if the player dies or reaches an ending. Walking through the scenes takes O(n) time at most, but searching the inventory makes the full function more expensive. 
If we keep collecting different items, each search has more items to check. That can make the total O(n^2), without counting repeated invalid input or the length of the text.

Todo14- printStats. We call countScenes, countEndings, and height, then print their answers. Each function takes O(n), so we do three O(n) traversals. 
This is still O(n), because the constant 3 doesn't change the complexity.

Todo15- printSpoilers. We call printEndings with the root and an empty path. This starts the recursive traversal, which visits the scenes in O(n) time. 
Making and printing the path strings adds extra work.

Todo16- printEndings. We check if the scene is an ending. If it is, we print its path and text. Otherwise, we recursively check each child, starting with choice 1. 
Each scene gets visited once, so the traversal is O(n). If we also count copying the path strings, it can take O(nh), where h is the tree's height. 
An ending still gets printed even if the player would die before reaching it, because this function checks the tree, not the player's Health.

Todo17- greedyRun. We follow one path and always pick the child with the most immediate loot. If two choices have the same loot, we keep the lower choice number.
We update HP and loot when entering each scene and stop at death or an ending. The scene traversal takes O(n) at most, since each scene has at most 3 choices to compare. 
Building longer path strings can add O(h^2) copying work over a path with h choices.

Todo18- bestLoot. We update HP for the current scene first. If it kills us, we return -1. If we survive and it's an ending, we return its loot and save the path. 
Otherwise, we recursively check the children and keep the best surviving result, then add the current scene's loot. The traversal is O(n), because each scene is checked once. 
Copying and saving the path strings can add O(nh) work.

Todo19- optimalRun. We call bestLoot from the root with the starting HP and an empty path. Then we print the best path and its loot. If bestLoot returns -1, we print that no path survives. 
This has the same O(n) scene traversal as bestLoot, along with the extra cost of copying paths and printing the result.

2..
The constructor sets an array when numChoices_ is greater than 0. A scene that has only one choice gets a single slot, instead of making it need 3.
If I did choices[3], it would waste memory for smaller scenes. An ending has choices_ that's equal to nullptr, because it has no children and doesn't need
an array.

3..
The destructor deletes each child pointer first. This calls the destructor for each child, and each descendant gets cleaned up. I also use delete[] choices_ to
free the array of the pointer slots and delete data_ for the scene. If we free the array first, and then tried to access choices_[i], I would be reading memory that already got freed
, which isn't the right behavior. Making every slot nullptr makes the cleanup safe and easy when it can't load correctly, before all the slots get filled.

4..
In my story, The Upstairs Light (someone give me a pulitzer) (jk), greedy takes path 1-1. It goes upstairs because that scene gives 8 loot, compared to 1 from the kitchen and 3 from staying on the porch.
Then it takes the cash beside the bed because that gives 15 more loot. It ends with 23 loot, but its HP becomes -2. So it found money, but it isn't exactly going to spend it... because the story is over.

Optimal takes path 2-1-1-1-1. It goes through the kitchen, pantry, narrow door, and cellar, then leaves without answering the voice. It gets 25 loot and survives with 5 HP. 
Greedy loses because it only looks at what it can get immediately. It doesn't consider the damage from the next scene or the bigger reward farther down another path. 
The upstairs route is supposed to be tempting, both because of the valuables and because the character hears their dad.

5..
Even though bestLoot checks every path, it doesn't start over from the root each time. Every scene has only one parent, so there isn't another branch that leads back to the same scene and makes us calculate it again. 
When paths share the beginning of the story, that beginning gets processed once before the recursive calls split up.

That means each scene gets visited once, so the traversal is O(n). A tree can have a lot of branches, but all of those scenes are already included in n. 
It can grow exponentially compared to its height, which isn't the same as being exponential compared to its number of scenes. Copying the path strings adds extra work in my implementation, but the scene visits themselves are still O(n).



6..
My game uses search, addFront, size, and print from List. We use search to check if an item is already in the bag. If it isn't, addFront adds it. We use size for the inventory count and print to show what the player collected.

In my story, both the kitchen and pantry give a House Key. Searching first means the player doesn't end up with two copies of it. 
If the bag has m items, one search takes O(m) in the worst case, since it might have to check every item. This assumes comparing the item names takes a fixed amount of time. As the bag grows, searching can take longer.

addFront and size take O(1), while print takes O(m). Since we add items to the front, the inventory prints them in the reverse order of when we picked them up. 
When the bag goes out of scope, its destructor uses isEmpty and deleteFront to clean everything up. My play function doesn't call those two methods directly.