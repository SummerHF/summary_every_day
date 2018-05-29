#CoreData
##Introduce
**xcdatamodeld**, 系统自动创建, 如果勾选了**Use Core Data**

* Entity: **Core Data**中的类定义. 典型的例子如一位雇员**Employee**或者一家公司**Company**. 在关系型数据库中, 一个实体对应于一张表**Table**.
* Attribute: 属性是附加到特定实体上的一部分信息. 例如, 一个雇员实体可以有雇员**姓名**, **位置**, 和**薪水**等属性. 在数据库中，属性对应于表中的**特定字段**.
* Relationship: 关系是多个实体之间的链接。在**Core Data**中，两个实体之间的关系称为**一对一**关系，而在一个实体和多个实体之间的关系被称为**一对多**关系。例如，一个经理与一组员工是**一对多**关系，而单个的员工与他的经理通常是**一对一******的关系。


```c
你可能已经注意到, 实体听起来很像类.同样，属性和关系听起来很像属性.有什么区别呢?您可以将Core Data实体看作是一个类的实例(You can think of a Core Data entity as a class definition and the managed object as an instance of that class )。
```
在**Core Data**中，属性可以是几种数据类型之一。


##Save Data Use Core Data

```c
 private func save(_ name: String) {
        guard let delegate = UIApplication.shared.delegate as? AppDelegate else { return }
        /// 1
        let managedContext = delegate.persistentContainer.viewContext
        /// 2
        let entity = NSEntityDescription.entity(forEntityName: "Person", in: managedContext)!
        /// 3
        let person = NSManagedObject(entity: entity, insertInto: managedContext)
        /// 4
        person.setValue(name, forKey: "name")
        /// 5
        do {
            try managedContext.save()
            people.append(person)
        } catch let error as NSError {
            print("Could not save. \(error), \(error.userInfo)")
        }
    }
```

##Fetch Data Use CoreData

```c
		guard let delegate = UIApplication.shared.delegate as? AppDelegate else { return }
        /// 1
        let managedContext = delegate.persistentContainer.viewContext
        /// 2 fetch request
        let fetchRequest = NSFetchRequest<NSManagedObject>(entityName: "Person")
        /// 3 fetch data
        do {
            people = try managedContext.fetch(fetchRequest)
        } catch let error as NSError {
            print("Could not fetch. \(error), \(error.userInfo)")
        }
```

* **NSManagedObjectContext**: An object representing a single object space or scratch pad that you use to fetch, create, and save managed objects
* **NSEntityDescription**: A description of an entity in Core Data
* **NSManagedObject**: A generic class that implements all the basic behavior required of a Core Data model object

```
A managed object is associated with an entity description (an instance of NSEntityDescription) that provides metadata about the object (including the name of the entity that the object represents and the names of its attributes and relationships) and with a managed object context that tracks changes to the object graph. It is important that a managed object is properly configured for use with Core Data. If you instantiate a managed object directly, you must call the designated initializer (init(entity:insertInto:)).
```

##NSManagedObjectSubclasses
A **UUID** type : universally unique identifier(全局唯一标示符)

**Integer 16, Integer 32 or Integer 64**
```
16, 32 and 64 refer to the number of bits representing the integer. This is important for two reasons: the number of bits reflects how much space an integer takes up on disk as well as how many values it can represent, or its range. Here are the ranges for the three types of integers: 
Range for 16-bit integer: -32768 to 32767 Range for 32-bit integer: –2147483648 to 2147483647 Range for 64-bit integer: –9223372036854775808 to 9223372036854775807 
How do you choose? The source of your data will dictate the best type of integer. 

```

**Allows External Storage**: When you enable Allows External Storage, Core Data heuristically decides on a per- value basis if it should save the data directly in the database or store a URI that points to a separate file. only useful for **Binary Data**.

###Storing non-standard data types in Core Data 
You can save any data type to Core Data (even ones you define) using the Transformable type as long as your type conforms to the **NSCoding protocol**. 

```c
The NSCoding protocol (not to be confused with Swift’s Codable protocol) is a simple way to archive and unarchive objects that descend from NSObject into data buffers so they can be saved to disk 

```

**@NSManaged**: Similar to @dynamic in Objective-C, the @NSManaged attribute informs the Swift compiler the backing store and implementation of a property will be provided at runtime instead of compile time. 
The normal pattern is for a property to be backed by an instance variable in memory. A property on a managed object is different: It’s backed by the managed object context, so the source of the data is not known at compile time 

a subclass of NSManagedObject
![](https://ws3.sinaimg.cn/large/006tKfTcgy1frs7hs50cfj30vb06yweq.jpg)
files of model
![](https://ws4.sinaimg.cn/large/006tKfTcgy1frs7iksydej30tg0hrq3r.jpg)

test code for saving data

```
    // Save the bow tie
    let bowtie = NSEntityDescription.insertNewObject(forEntityName: "Bowtie", into: self.persistentContainer.viewContext) as! Bowtie
    bowtie.name = "My bow tie"
    bowtie.lastWorn = NSDate()
    saveContext()

    // Retrives test bow tie
    let request = NSFetchRequest<Bowtie>(entityName: "Bowtie")
    if let ties = try? self.persistentContainer.viewContext.fetch(request) , let tiesName = ties.first?.name {
      print("\(tiesName)")
    } else {
      print("Test Error")
    }
```

##The Core Data Stack
* **NSManagedObjectModel**
* **NSPersistentStore**
* **NSPersistentStoreCoordinator**
* **NSManagedObjectContext**

