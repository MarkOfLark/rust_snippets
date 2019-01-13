# Traits
## Traits as Interfaces

Traits are similar to interfaces in other languages.

HOW ARE TRAITS DIFFERENT?

## Implement Traits From Different Modules
```rust
struct Point2d
{
    x: f32,
    y: f32,
}



impl Point2d
{
    fn distance_to(&self, other: &Point2d) -> f32 {
        ((self.x - other.x).powf(2.0) + (self.y - other.y).powf(2.0)).sqrt()
    }
}

fn main() {
    let p1 = Point2d{ x:-1.0, y: 2.0 };
    let p2 = Point2d{ x: 0.0, y: 3.0 };

    println!("{}", p1.distance_to(&p2));
}
```

But what could we do to make this more flexible? Implement the distance function for a trait that serves as the interface to a type.

```rust
#struct Point2d
#{
#    x: f32,
#    y: f32,
#}

trait ToPoint2d {
    fn to_point_2d(&self) -> &Point2d;
}

impl ToPoint2d for Point2d {
    fn to_point_2d(&self) -> &Point2d {
        self
    }
}

impl Point2d
{
    fn distance_to<P: ToPoint2d>(&self, other: &P) -> f32 {
        let other2d = other.to_point_2d();
        ((self.x - other2d.x).powf(2.0) + (self.y - other2d.y).powf(2.0)).sqrt()
    }
}

#fn main() {
#    let p1 = Point2d{ x:-1.0, y: 2.0 };
#    let p2 = Point2d{ x: 0.0, y: 3.0 };
#
#    println!("{}", p1.distance_to(&p2));
#}
```

Now we can implement the trait on any other type (even from different modules) and still use the distance function has before.

```rust
##[derive(Clone)]
#struct Point2d
#{
#    x: f32,
#    y: f32,
#}
#
#trait ToPoint2d {
#    fn to_point_2d(&self) -> Point2d;
#}
#
#impl ToPoint2d for Point2d {
#    fn to_point_2d(&self) -> Point2d {
#        (*self).clone()
#    }
#}
#
#impl Point2d
#{
#    fn distance_to<P: ToPoint2d>(&self, other: &P) -> f32 {
#        let other2d = other.to_point_2d();
#        ((self.x - other2d.x).powf(2.0) + (self.y - other2d.y).powf(2.0)).sqrt()
#    }
#}
#
struct Point3d
{
    x: f32,
    y: f32,
    z: f32,
}

impl ToPoint2d for Point3d {
    fn to_point_2d(&self) -> Point2d {
        Point2d{ x: self.x, y: self.y }
    }
}

fn main() {
    let p1 = Point2d{ x:-1.0, y: 2.0 };
    let p2 = Point3d{ x: 0.0, y: 3.0, z: -1.0 };

    println!("{}", p1.distance_to(&p2));
}
```

But this is only half the story because if we call it the other way around it doesn't compile.

```rust
##[derive(Clone)]
#struct Point2d
#{
#    x: f32,
#    y: f32,
#}
#
#trait ToPoint2d {
#    fn to_point_2d(&self) -> Point2d;
#}
#
#impl ToPoint2d for Point2d {
#    fn to_point_2d(&self) -> Point2d {
#        (*self).clone()
#    }
#}
#
#impl Point2d
#{
#    fn distance_to<P: ToPoint2d>(&self, other: &P) -> f32 {
#        let other2d = other.to_point_2d();
#        ((self.x - other2d.x).powf(2.0) + (self.y - other2d.y).powf(2.0)).sqrt()
#    }
#}
#
#struct Point3d
#{
#    x: f32,
#    y: f32,
#    z: f32,
#}
#
#impl ToPoint2d for Point3d {
#    fn to_point_2d(&self) -> Point2d {
#        Point2d{ x: self.x, y: self.y }
#    }
#}
#
fn main() {
    let p1 = Point2d{ x:-1.0, y: 2.0 };
    let p2 = Point3d{ x: 0.0, y: 3.0, z: -1.0 };

    println!("{}", p2.distance_to(&p1));
}
```

Lets fix that...

```rust
##[derive(Clone)]
#struct Point2d
#{
#    x: f32,
#    y: f32,
#}
#
#trait ToPoint2d {
#    fn to_point_2d(&self) -> Point2d;
#
#    fn distance_to<P: ToPoint2d>(&self, other: &P) -> f32 {
#        let a = other.to_point_2d();
#        let b = self.to_point_2d();
#        ((a.x - b.x).powf(2.0) + (a.y - b.y).powf(2.0)).sqrt()
#    }
#}
#
#impl ToPoint2d for Point2d {
#    fn to_point_2d(&self) -> Point2d {
#        (*self).clone()
#    }
#}
#
#impl Point2d
#{
#    fn distance_to<P: ToPoint2d>(&self, other: &P) -> f32 {
#        let other2d = other.to_point_2d();
#        ((self.x - other2d.x).powf(2.0) + (self.y - other2d.y).powf(2.0)).sqrt()
#    }
#}
#
#struct Point3d
#{
#    x: f32,
#    y: f32,
#    z: f32,
#}
#
#impl ToPoint2d for Point3d {
#    fn to_point_2d(&self) -> Point2d {
#        Point2d{ x: self.x, y: self.y }
#    }
#}
#
fn main() {
    let p1 = Point2d{ x:-1.0, y: 2.0 };
    let p2 = Point3d{ x: 0.0, y: 3.0, z: -1.0 };

    println!("{}", p2.distance_to(&p1));
}
```

Now lets implement our trait for the built in tuple type.

```rust
##[derive(Clone)]
#struct Point2d
#{
#    x: f32,
#    y: f32,
#}
#
#trait ToPoint2d {
#    fn to_point_2d(&self) -> Point2d;
#
#    fn distance_to<P: ToPoint2d>(&self, other: &P) -> f32 {
#        let a = other.to_point_2d();
#        let b = self.to_point_2d();
#        ((a.x - b.x).powf(2.0) + (a.y - b.y).powf(2.0)).sqrt()
#    }
#}
#
#impl ToPoint2d for Point2d {
#    fn to_point_2d(&self) -> Point2d {
#        (*self).clone()
#    }
#}
#
#impl Point2d
#{
#    fn distance_to<P: ToPoint2d>(&self, other: &P) -> f32 {
#        let other2d = other.to_point_2d();
#        ((self.x - other2d.x).powf(2.0) + (self.y - other2d.y).powf(2.0)).sqrt()
#    }
#}
#
#struct Point3d
#{
#    x: f32,
#    y: f32,
#    z: f32,
#}
#
#impl ToPoint2d for Point3d {
#    fn to_point_2d(&self) -> Point2d {
#        Point2d{ x: self.x, y: self.y }
#    }
#}
#
impl ToPoint2d for (f32,f32) {
    fn to_point_2d(&self) -> Point2d {
        match self {
            (x,y) => Point2d{ x: *x, y: *y}
        }
    }
}

fn main() {
    let p1 = Point2d{ x:-1.0, y: 2.0 };
    let p2 = Point3d{ x: 0.0, y: 3.0, z: -1.0 };
    
    println!("{}", p1.distance_to(&(0.0,3.0)));
    println!("{}", (-1.0,2.0).distance_to(&p2));
}
```