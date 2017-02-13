----------------------------------------------------------------------------------------------------------------
Title: The Observer Pattern

Sources:
Notes below regarding observer pattern taken from "Design Patterns - Elements of Reusable Object-Oriented Software"
By Gamma, Helm, Johnson, Vlissides

Example Code provided by Derek Banas:
Tutorial: https://www.youtube.com/watch?v=wiQdrH2YpT4
Source Code: http://www.newthinktank.com/2012/08/observer-design-pattern-tutorial/

Author: Justin J

Purpose: FAU Object Oriented Software Design Course, Sprint 2017

----------------------------------------------------------------------------------------------------------------

Intent
- define a one to many dependency between objects so that when one object changes state, all dependents are notified
  and updated automatically

Motivation
- by dividing system into many subsystems and classes, there is need to maintain consistency between related objects
- need to maintain consistency without tight coupling
- in GUI there may be any number of different UIs that display the same data
- subject may have any number of dependent observers
- observer is notified when subject undergoes a change in state, observer then queries subject to synchronize
- interaction is also known as publish-subscribe: subject is publisher of notifications, any number of observers can subscribe

Applicability
- an abstraction has two aspects, one dependent on the other. Encapsulating these aspects in separate objects allows for reuse
- a change in one object requires changing others, and you don't know how many objects need to be changed
- when object should be able to notify other objects without making assumptions about who these objects are

Structure
- see observerpattern.png

Participants
- subject
	- knows its observers
	- provides an interface for attached/detaching observer objects
- observer
	- defines an updating interface for objects that should be notified of changes in a subject
- concrete subject
	- stores state of interest to Concrete Observer objects
	- sends a notification to its observers when its state changes
- concrete observer
	- maintains a reference to a Concrete Subject object
	- stores state that should stay consistent with subject
	- implements the observer updating interface to keep its state consistent with subject
	
Collaborations
- concrete subject notifies its observers whenever a change occurs that cold make observers state inconsistent with its own
- once informed of change in concrete subject, a concrete observer may query the subject for more information
- concrete observer uses this information to reconcile its state with subject
- see observerpatternsequencediagram.png

Consequences
- observer patter lets you vary subjects and observers independently, allowing for reuse of both
- abstract coupling between subject and observer
	- all subject knows is that it has a list of observers that conform to interface of abstract observer class, coupling is abstract and minimal
	- subject and observer can belong to different layers of abstraction in a system
- support for broadcast communication
	- notification that subject sends isn't required to specify receiver
	- notification is broadcast automatically to all interested objects that are subscribed
	- freedom to add/remove observers at any time, up to observer to handle or ignore a notification
- unexpected updates
	- observers have no knowledge of each other, they can be blind to ultimate cost of changing the subject
	- observers may be forced to work hard to deduce the changes in subject
 
Implementation
- mapping subjects to their observers
	- simplest solution is for subject to store references to all observers in the subject
	- if memory becomes an issue, trade off space for time and use associate look up (hash table) to maintain subject to observer mapping
- observing more than one subject
	- there may be cases where observer depends on more than one subject (ex Spreadsheet depends on more than 1 data source)
	- must extend the update interface in such cases, to let the observer know which subject is sending the notification
		- subject can pass itself as parameter in Update operation, letting observer know which subject to examine
- who triggers the update?
	- 1st Option - state setting operatons on Subject call notify after they change the subject's state
		- advantage is that clients don't have to remember to call Notify on the subject
		- disadvangtage is that several consecutive operations will cause several consecutive updates, possibly inefficient
	- 2nd Option - Make clients responsible for calling Notify at the right time
		- advantage is that client can wait to trigger the update until after a series of state changes has been made, avoiding needless updates
		- disadvantage is that clients have an added responsibility to trigger the update, more likely to lead to errors
- dangling references to deleted subjects
	- deleting a subject should not produce dangling references to its observers
	- one solution is to force subject to notify observers as it is deleted, allowing observers to reset references
 	- simply deleting observers is not an option,because other objects may reference them, or they may be observing other subjects
- making sure subject state is self consistent before notification
	- send notifications from template methods in abstract subject classes, make the notify operation the last operation in template method
- avoiding observer-specific update protocols: the push and pull models
	- push model: subject sends observers detailed info of change, can have negative impact on observers reusability
	- pull model: subject sends no info of change and expects observer to query back for details, may be inefficient
		- need a balance
- specifying modifications of interest explicitly
	- improve update efficiency by extending subject's registration interface to allow registering observers only for specific events of interest
	- when such an event occurs, subject only notifies observers that are registered in that event
	- at notification time, subject supplies the changed aspect to its observers as a parameter in update operation
- encapsulating complex update semantics
	- if subject/observer dependency relationship is very complex, a ChangeManager object may be required to maintain relationship between them
		- it minimizes the work required to make observers reflect a change in their subject
		- ChangeManager maps subject to observer and provides interface to maintain mapping, eliminates need for subject to maintain referecne
		- ChangeManager defines a particular update strategy
		- ChangeManager updates all dependent observers at request of subject
 		- ChangeManager is an instance of Mediator pattern
 - combining subject and observer classes
 	- class libraries written in languages that lack multiple inheritance (smalltalk) generally dn't define separate Subject and Observer classes
 		- combine their interfaces in one class
	
	
Related Patterns
- mediator pattern, singleton pattern
          	