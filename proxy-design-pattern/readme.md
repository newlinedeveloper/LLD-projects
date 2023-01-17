The Proxy design pattern is a structural pattern that provides a surrogate or placeholder for another object to control access to it. In other words, it acts as an intermediary between the client and the target object, and it can be used to add extra functionality or behavior to the target object.

In Java, the proxy pattern can be implemented by creating a proxy class that implements the same interface as the target class and holds a reference to an instance of the target class. The proxy class can then override or extend the methods of the target class to add the desired functionality.

Here is an example of how the proxy pattern can be implemented in Java:


```

interface Image {
    void display();
}

class RealImage implements Image {
    private String file;
 
    public RealImage(String file) {
        this.file = file;
        load();
    }
 
    private void load() {
        System.out.println("Loading " + file);
    }
 
    public void display() {
        System.out.println("Displaying " + file);
    }
}

class ImageProxy implements Image {
    private RealImage image;
    private String file;
 
    public ImageProxy(String file) {
        this.file = file;
    }
 
    public void display() {
        if (image == null) {
            image = new RealImage(file);
        }
        image.display();
    }
}



```

In this example, the RealImage class represents the target object, and the ImageProxy class represents the proxy. The Image interface is implemented by both classes, and the ImageProxy class holds a reference to an instance of the RealImage class. The display() method of the ImageProxy class checks whether the RealImage instance has been created, and if not, it creates a new instance before calling the display() method of the RealImage class.

This example shows how the proxy pattern can be used to control access to the target object and to add extra functionality such as lazy loading. In this case, the real image is only loaded when the display method is called for the first time, this way we save memory and resources.

There are different types of proxy such as Remote, Virtual, Protection, Smart and more. Each of them serves different purposes and addresses specific needs
