# How to create abstraction
expressivity of the abstraction seems to be a trade off
- more expressive
    - usually means interacting with procedural primitives
        - this assumes most computations are procedural at the lowest level. so allowing to operate at lower level means exposing more procedurals to users. (this might not be the case for GPU programming)
    - steep learning curve
    - fewer opportunities of auto-optimization
        - user sometimes do a worse job at optimization than they think they are
- less expressive
    - hide complexity behind a declarative interface