`Enable codec2 hidl in manifest.xml`: ./device/generic/common/manifest.xml

manifest.xml only has entry point of hidl service, no impl.


frameworks/base/core/java/android/app/LoadedApk.java
 873         // Add by AshyEarl
 874         libraryPermittedPath += File.pathSeparator + "/system/lib64/";
 875         libraryPermittedPath += File.pathSeparator + "/vendor/lib64/";
 876         libraryPermittedPath += File.pathSeparator + "/system/lib64/dri/";

https://elinux.org/images/d/d6/Android_common_kernel_and_out_of_tree_patchset.pdf

https://ci.android.com/builds/submitted/8626023/linux/latest/view/soong_build.html

https://lwn.net/

```plantuml
@startuml
    skinparam backgroundColor #EEEBDC
    skinparam handwritten true
    actor Customer
    Customer -> "login()" : username & password
    "login()" -> Customer : session token
    activate "login()"
    Customer -> "placeOrder()" : session token, order info
    "placeOrder()" -> Customer : ok
    Customer -> "logout()"
    "logout()" -> Customer : ok
    deactivate "login()"
@enduml
```

### uml: class diagram
```plantuml
@startuml
package "customer domain" #DDDDDD {
    class Contact {
        + email
        + phone
    }

    class Address {
        + address1
        + address2
        + city
        + region
        + country
        + postalCode
        + organization
    }

    note right of Address 
        There are two types of 
        addresses: billing and shipping
    end note

    class Customer {
    }

    Customer *-- Contact
    Customer *-- ShippingAddress
    Customer *-- BillingAddress
    Customer *--{ SalesOrder

    class ShippingAddress <<Address>>
    class BillingAddress <<Address>>
    class SalesOrder {
        + itemDescription
        + itemPrice
        + shippingCost
        + trackingNumber
        + shipDate
    }
}
@enduml
```

## uml: state diagram
```plantuml
@startuml
scale 600 width
skinparam backgroundColor #FFEBDC
[*] -> Begin
Begin -right-> Running : Succeeded
Begin --> [*] : Aborted
state Running {
  state "The game runneth" as long1
  long1 : Until you die
  long1 --> long1 : User interaction
  long1 --> keepGoing : stillAlive
  keepGoing --> long1
  long1 --> tooBadsoSad : killed
  tooBadsoSad --> Dead : failed
}
Dead --> [*] : Aborted
@enduml
```