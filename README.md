# About Types

## kinds of types

Julia has __abstract__ and __concrete__ types.  A given abstract type is a collective label for one or more other abstract and/or concrete types. Another way to look at it:  you can collect concrete types under the umbrella of an abstract type, and you can subspecify an abstract type through subordinate abstract types.

```julia
                                             # abstract types

abstract type GeometricEntity end            # GeometricEntites may exist
abstract type Point <: GeometricEntity end   # All Points are GeometricEntities
abstract type Segment <: GeometricEntity end # All Segments are Geometric Entities

                                       # concrete types

struct Point2D <: Point                # Each Point2D is a Point & a GeometricEntity
    x::Float64                         # there is a field named `x`, a Float64 value
    y::Float64                         # there is a field named `y`, a Float64 value
end

struct Point3D <: Point                # Each Point3D is a Point & a GeometricEntity
    x::Float64                         # there is a field named `x`, a Float64 value
    y::Float64                         # there is a field named `y`, a Float64 value
    z::Float64                         # there is a field named `z`, a Float64 value
end

```



```julia
# tell Julia how to construct tuples from points
Base.Tuple(point::Point2D) = (point.x, point.y)
Base.Tuple(point::Point3D) = (point.x, point.y, point.z)

# define useful constant points
const Origin2D = Point2D(0.0, 0.0)
const Origin3D = Point3D(0.0, 0.0, 0.0)

struct Segment2D <: Segment
    start::Point2D
    finish::Point2D
end

#  define the length of a segment
using LinearAlgebra                                             # for `norm`
length(x::Segment) = norm(Tuple(x.finish) .- Tuple(x.start))    # `-` coordinate-wise

# construct a segment
segment = Segment2D(Origin2D, Point2D(1.0, 1.0))
# find the length
length(segment)
1.4142135623730951
```



Concrete types can have one or more parameters.  Here is a common use pattern (restart Julia).

```julia
struct Point2D{T}  # T specializes to the type of x and y as the Point2D is constructed
   x::T
   y::T
end

param(pt::Point2D{T}) where {T} = T

int_coords = Point2D(1, 2)
float_coords = Point2D(1.0, 2.0)

param(int_coords)
Int64
```

Abstract types can have parameters too.  A use is to carry back specific field type information, helpful with multidispatch.

```julia
abstract type Abstract{T} end

struct Concrete{T} <: Abstract{T}
    value::T
end

x = Concrete(7)
y = Concrete(7.0)

supertype(typeof(x))
Abstract{Int64}

# The `T` here is unrelated to the `T`s above, it is a placeholder
multidispatch(x::T) where {T<:Abstract{Int64}} = "field is an Int64"
multidispatch(x::T) where {T<:Abstract{Float64}} = "field is a Float64"

multidispatch(x)
"field is an Int64"
multidispatch(y)
"field is a Float64"

```

