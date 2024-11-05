# Coordinate Systems Specification
To battle the imprecision of floats, the game will utilise 4 coordinate systems for physics.

## Coordinate systems overview
### Renderspace
This is the coordinate system in which rendering and local physics happen. It is centered on the camera, and the unit is meters.

It is a system of cartesian coordinates, where the camera's viewport sets the x and y coordinates, with z being the depth.

### Planetspace
This is the coordinate system in which the precise position of a ship is calculated. It is centered on the gravitationally most significant planet, and the unit is $2^{10}$ meters, or a unit of $2^i$ meters, where $i \in \mathbb{N}$ and $i \gt 10$. Each spacecraft is one object.

It is a system of cartesian coordinates, where the planet's north pole is up, and one coordinate is a tangent to the planet's orbit.

### Starspace
This is the coordinate system representing the position in the star system. It is centered on the star, and the unit is $2^{37}$ meters ($128\cdot1024\cdot1024$). Each planet system is one object.

It is a system of cartesian coordinates, where the star's north pole is up, and one coordinate is a tangent to the star's orbit.

### Galaxyspace
This is the coordinate system representing the position in the galaxy. It is centered on the center of the galaxy, and the unit is $2^{53}$ meters ($4\cdot1024^4$). Each star system is one object.

It is a system of cartesian coordinates, where the up coordinate is normal to the galactic plane.

## Spacecraft behavior
The spacecraft's position is calculated in either planetspace, starspace, or galaxyspace, depending on what is the most gravitationally significant body at the moment.

## The maths
### Rendering
For performance reasons, only the closest planet system (if applicable) and stars will be rendered along with the ship.

Rendering the ship is straightforward, however not so much for the celestial bodies. Since celestial bodies are very large, we can set their size to 1m on every axis in the renderspace, and divide their distance from the camera by the original size, such that the result is in meters. This relies on the fact that a body of the same size, twice as far away, will appear to be half the size.

For bodies above a certain visual size, this rendering rule does not apply anymore, and they are instead rendered as close-up.

### Physics
Calculations shall happen "locally", in the most detail suitable. Let's list a few cases that might arise, as an example:

1. A spacecraft is in a low orbit around Earth.

In this case, the spacecraft will be calculated as an object in Earth's planetspace, but won't be distinguishable from Earth itself in starspace.

2. A spacecraft is on the surface of the Moon.

In this case, the spacecraft will still be calculated in planetspace, however, depending on the implementation, said planetspace may not be of the Earth, but of the Moon (see [implementation considerations](#implementation-considerations))

3. A spacecraft is in interplanetary space around the Sun.

In this case, the spacecraft will be treated as a planet, being calculated in starspace.

4. A colonial ship is in interstellar space.

In this case, the ship will be treated as a star, being simulated in galaxyspace.

## Implementation considerations
### Nested planetspaces
It might, depending on the choice of data type for coordinates, be desirable to implement nested planetspaces for moons. This could, for example, be implemented as an array/list of planetspaces inside a planetspace object.

### Binary systems
Binary systems are most likely best implemented as a system center, with the two stars or planets being calculated in starspace or planetspace, respectively.

For instance, the Pluto-Charon binary system could be implemented as a system center in starspace, and separate celestial bodies inside planetspace.

This approach would effectively require no special approach on the side of the simulation.