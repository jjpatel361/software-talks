## A Plea for Lean Software - Niklaus Wirth 1995

The paper answers the question given the processing power has increased so much why the software today still lags. Compare a text editor written in 90's vs a modern day text editor. The modern one takes up more memory few MB's (x1000 times more). Given the new memory consumption we can expect a superlative performance text editing process. On contrary reality is, modern text editor is less usable (_debatable as there isn't a metric to measure usability_), slower and bloated.



**How is Software explosion possible?**

The processor technology has increased performace vs price ratio. E.g performance gain of v1 to v5 is 300 times whereas price gain is 3x. So this has led to principles

- Software expands to fill up available memory (Parkinson)
- Software is getting slower more rapidly than hardware becomes faster (Resier)

So where is the all new performance boost consumed ? Answer is, less attractive feature sets e.g. fancy UI icons, windows etc. These add lesser direct value to product and more complexity to software. 

**Causes of FAT Software**

1. Rapidly growing hardware. There is an underlying assumption that hardware WILL grow. We can also model this as *rent* cost, consider running a software for 1 hour like renting a space. Longer the app runs on more capacity higher the rent. This rent isn't paid by software vendors instead passed on to the owners of hardware to upgrade and buy more land (powerful hardware). Hence, software vendors have lesser incentive to optimize for rents and more incentivized for features. A new version release, leads to add a new room next to existing house. If we were to optimize rent, then we would be adding new room on the top of existing space.

2. Customer ignorance feature essential vs nice to have. Software vendors are no longer provide conditional updates / feature flagging / plugin based ecosystem to allow granular control. VIM is a good example. Basic editor with conditonal plugins allowing users to fine grain control over VIM experience. Newer releases with more features are better than releases with performance updates. Why performance updates have lesser impact ?
  - Users are already accustomed to an existing average experience and less incentive for vendor to spend money on. E.g. rent will be paid by the owner remember! 
  - UI and feature changes are more visible hence excites the end user. A performance update unless 2x isn't really visible.

**Only Feature > Complexity**

If we go on adding more feature set it will be add more complexity. There are two types of complexities
    - **inherent complexity**: huge sparse matrix multiplication is inherent complex needs powerful hardware to solve
    - **self inflicted complexity** : a pop window feature, new UI icon sets using external libs which adds to the bloat. We could have built a lightweight feature or do things to fine grain product requirements. These ones make the software complex for the vendors thus increasing maintenance cost, frequency of updates etc.


Other similar reads 

- Economic theory [Jevons paradox](https://en.wikipedia.org/wiki/Jevons_paradox)
- Statement [Andy and Bill Law](https://en.wikipedia.org/wiki/Andy_and_Bill%27s_law)  