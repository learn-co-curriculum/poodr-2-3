# POODR
## Chapter 2: Designing Classes with a Single Responsibility
- basics of OO
  - foundation is message
  - organizational structure is the class
- goals in making classes
  - make your application do what it is supposed to right now
  - make your code easy to change later
  
#### Deciding What Belongs in a Class
- grouping methods together into classes affects how your application can grow later
- `Design is more the art of preserving changeability than it is the act of achieving perfection`
- organize your code to allow for easy change, which means:
  - changes have no unexpected side effects
  - small changes require small code
  - existing code is easy to reuse
  - easiest way to make a change is to add easily changeable code
- code should be TRUE:
  - transparent: consequences of change should be obvious
  - reasonable: cost of changes should be proportional fo the benefits
  - useable: existing could should be usable in new contexts
  - exemplary: code should encourage others to perpetuate these qualities

#### Creating Classes That Have a Single Responsibility
- a class should do the smallest possible useful thing
- bicycle example:
  - bicycles have gears (slow but easy and hard but fast)
  - gears control how many times the wheels rotate for each pedal rotation
  - chainring:cog ratio
- nouns represent obvious candidates for classes
  - classes only make sense when the noun has behavior to encapsulate
    - bicycle is a noun, but what is the behavior?
    - gears have chainrings (attr), cogs(attr) and ratios(meth)
- classes consist of everything they implement + everything they inherit
- adding new functionality:
  - bicycles with same gearing can have different wheel sizes (bigger wheels travel farther per rotation)
    - gear inches(meth) = wheel diameter * gear ratio
    - wheel diameter(meth) = rim diameter(attr) + twice tire diameter(attr)
  - gears now require additional args to initialize so preexisting code breaks
- SRP matters because code is easier to reuse and less likely to break
  - `Reusable classes are pluggable units of well-defined behavior that have few entanglements.`
    - an app should be like a box of blocks you can select and assemble
    - a class that does several things is harder to reuse because behaviors are paired together
      - duplicating part of the code makes it harder to maintain
      - using only one behavior of a class is improper and your code is likely to break when the class is refactored
- determining if your class has SRP:
  - ask your instance questions about itself
  - describe it in one sentence with no conjunctions
  - a class is cohesive if everything is related to its central purpose
  - Responsibility-Driven Design:
    - a class has responsibilities that fulfill its purpose
  - gear class responsibility:
    - calculate the ratio between two toothed sprockets?
    - calculate the effect that a gear has on a bicycle?
    - gear class does too much
- when to make design decisions
  - don't decide too soon
  - new feature requests come frequently
  - what is the future cost of doing nothing today?
  - structure of every class is a message to future maintainers
  - minimize costs by making informed tradeoffs between needs of present and possibilities of the future
  - we don't know enough about our future app to decide about gear
  
#### Writing Code That Embraces Change
- depend on behavior, not data
  - behavior should live in only one place
    - DRY code tolerates change because you only have to update it once
  - data in instance variables should always be wrapped in accessor methods
    - data becomes behavior when it is only accessed via methods
    - can change visibility via private methods
    - less distinction between data and objects
  - complex data structures are bad
    - requires multiple methods to know how to use it (not DRY)
    - any change to the data structure has cascading effects
    - obscures what the data really is
  - separate structure from meaning by wrapping in a Struct
    - bundle attributes using accessor methods without a class
    - protects against changes in externally owned data
    - code becomes readable with clear intentions
- Enforce SRP Everywhere
  - methods should also have SRP for the same reasons
  - benefits:
    - expose previously hidden qualities
      - gear calculating wheel diameter suggests wheel should own the behavior
      - new request for bicycle wheel circumference suggests wheel deserves a class
    - clear method names reduce comments
    - encourages reuse
    - easier to move small methods

#### Summary
- changeable and maintainable OO code begins with SRP classes
- classes can isolate their single responsibility from the rest of the app
- isolation allows change without consequence and reuse without duplication


## Chapter 3: Managing Dependencies
- OO claims to be effective because it models reality
  - objects reflect qualities of a real-world problem
  - object interactions provide solutions
  - objects must collaborate to get more information
- messages are objects invoking behavior from:
  - themselves (chapter 2)
  - inheriting it (chapter 6)
  - another object (right now!)
  
#### Understanding Dependencies
- an object is dependent when:
  - it knows the name of another class
  - the name of a message it sends to another instance
  - the arguments a message requires
  - the order of arguments
- dependencies mean a change to one class requires change to another class
- each class should know just enough to do its job
- coupling between objects (CBO)
  - gear couples to wheel so it is difficult to reuse without wheel
- message chaining couples every object on the path
- coupling tests too tightly to code makes testing expensive

#### Writing Loosely Coupled Code
- dependency injection
  - instead of gear knowing about wheel class, have it know a specific object
  - hard-coding a classname limits collaboration to only that class
  - attachment to type over message
    - duck-typing: we only care what messages the object responds to
    - gear only cares if the object has a diameter
- isolate dependencies
  - isolate instance creation
    - inject to break dependency if possible
    - move any dependency creation to initialize to better expose it
    - lazy create in its own method 
  - isolate vulnerable external messages
    - encapsulate in their own methods
- remove argument order dependencies by using keyword arguments
  - can pass in any order 
  - can add or remove arguments and defaults with no worries of side effects
  - pay for this future flexibility with verbosity
  - gained dependency on keyword names
  - okay to mix and match in an application
- isolate multi-parameter initialization with factories
  - if you can't avoid positional args, make a wrapper method to DRY it
    - use a module to make it clear the wrapper does not have instances
    
#### Managing Dependency Direction
- choosing dependency direction to depend on things that change less
  - some classes are more likely to have changes
  - concrete classes are more likely to change then abstract
  - changing a class with more dependents will result in more consequences
- understanding the likelihood of change
  - ruby bas classes change less often than your own
  - framework classes (for stable frameworks) will change less often than your own
- recognizing concretions and abstractions
  - we abstracted gear to depend not on a wheel but on a diameter message
    - created an interface that is an abstraction of an idea that a category of items have diameters
  - abstractions represent common qualities less likely to change
- dependent-laden classes are rigid because they are under pressure not to change
- finding the dependencies that matter 
  - A - abstract zone: changes unlikely but have broad effects
  - B - neutral zone: changes unlikely and have few side effects
  - C - neutral zone: changes likely and have few side effects
  - D - danger zone: changes likely and will cascade 
    - costly and difficult to change
    
#### Summary
- dependency management is core to creating future-proof apps
- inject dependencies creates loosely-coupled objects that can be reused
- isolating dependencies allows objects to adapt
- depending on abstractions decreases the likelihood of change
- classes should depend on code that changes less often than it
