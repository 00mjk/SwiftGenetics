# SwiftGenetics
## by Santiago Gonzalez
### ***A pure-Swift genetic algorithm library built for composition and extensibility.***

**SwiftGenetics** is a genetic algorithm library that has been engineered from-scratch to be highly extensible and composable, by abstracting away different pieces of functionality, while providing concrete implementations of certain use cases, such as tree-based genomes. **SwiftGenetics** is written in pure Swift, and makes proper use of functional programming and Swift's wonderful type system. This project is provided under the MIT License (see the `LICENSE` file for more info).

**SwiftGenetics** can be added to your project as a dependency with the Swift Package Manager.

## Functionality

**SwiftGenetics** provides an abstracted base for genetic algorithms, intended to support different representations and genetic operators.

### Abstractions

* **Gene** Represented by the `Gene` protocol, genes represent an individual piece of a genome.
* **Genome** A collection of genes, represented by the `Genome` protocol.
* **Organism** An individual in the population, represented by the `Organism` class (we want reference semantics here), and generic upon a `Genome` subtype, where the organism's genotype is an instance of the type that conforms to `Genome` .
* **Population** The world in which organisms evolve, represented by the `Population` class.
* **Environment** A configuration which is disseminated down to individual genes and controls various behaviors such as mutation, recombination, and selection. You can modify environments as a GA is running.
* **Clade** A "kind" of genetic algorithm (e.g., bit strings and trees might be two kinds clades). Clades can be built from broader clades to get more complex functionality. Clades conform to the `Genome` protocol. Clades are intended to be generic and composable. A specific GA can be thought of as being a "concretized" clade type (e.g., a tree of arithmetic operators).

### Generic Implementations

* **Living Strings** Homogeneous sequences of genes. A classic within the world of genetic algorithms.
* **Living Trees** Trees whose structure and node types are evolved.
* **Living Forests** A fixed-size collection of coevolved trees. Evolution of Living Forests is analogous to evolution of a genome of chromosomes.

The library also provides the following primitives that you can use to build your own complex genomes:

* `ContinuousGene` to represent a single floating-point value.
* `DiscreteChoiceGene` to represent a discrete choice in a set.


## Usage

Everything you need to use **SwiftGenetics** is in the `Sources/` directory. The main entry point you need is `Population`. Generally, you can use **SwiftGenetics** as follows:

1. Build a `Population`.
2. Call the `epoch()` method on your population.
3. Set the fitness for each `Organism` in the population's `organisms` array.
4. Repeat steps 2 and 3 *ad infinitum* (or until you're happy with a solution).

### Evolution Wrappers

Oftentimes you might have long-running fitness calculations that you want to run concurrently, in which case you can use `EvolutionWrapper` types as the entry point into **SwiftGenetics**. `ConcurrentSynchronousEvaluationGA` and `ConcurrentAsynchronousEvaluationGA` perform synchronous and asynchronous, respectively, fitness evaluations. These wrappers make adding a GA trivial, all you need are an initial population and a type that conforms to `EvolutionLoggingDelegate`.

One cool feature that `ConcurrentAsynchronousEvaluationGA` has is the ability to detect and efficiently handle duplicate genomes within a generation (based on their hash value from a conformance to `Hashable`).

An end-to-end example of evolution wrapper use can be found in `Tests/SwiftGeneticsTests/Integration/GeneticAlgorithmIntegrationTests.swift`. Specifically, the `testSortingGA` integration test shows how **SwiftGenetics** can be used to evolve vectors of continuous values.

### Checkpointing

Every state object in **SwiftGenetics** conforms to the Swift `Codable` protocol, allowing serialization and deserialization. The `CheckpointManager` provides a lovely abstraction for this that allows writing and reading checkpoints in one line:

```swift
typealias G = ... // A concrete Genome type.
let population: Population<G> = ...
let checkpointFile: URL = ...
// Save the population to a checkpoint
try CheckpointManager<G>.save(population, to: checkpointFile)
// Build a new population from the checkpoint.
let reconstitutedPopulation = try CheckpointManager<G>.population(from: checkpointFile)
```

### Concrete Example

```swift
// Example coming soon!
```

### Defining a Fitness Evaluator

Genetic algorithms, as a black box optimizer, aim find the global maximum, so your fitness function must be formulated so that larger values are better. Types can conform to the `SynchronousFitnessEvaluator` or `AsynchronousFitnessEvaluator` protocols as a way to provide fitness values.

```swift
struct ExampleFitnessEvaluator: SynchronousFitnessEvaluator {
	typealias G = MyGenomeType
	func fitnessFor(organism: Organism<G>, solutionCallback: (G, Double) -> ()) -> Double {
		// Example coming soon!
	}
}
```

### Dependencies

There are no external dependencies, yeehaw! On macOS, you can get off the ground running. Linux is supported out-of-the-box too!

## Citing

If you use **SwiftGenetics** in a publication, you can use the following BibTeX entry to cite this project:

```
@misc{swiftgenetics,
	Title = {SwiftGenetics},
	Author = {Santiago Gonzalez},
	howpublished = {\url{https://github.com/sgonzalez/SwiftGenetics}}
}
```

## Future Work

* More flexibility.
* More extensibility.
* More concrete implementations.
* Support for dominance relations, like in NSGA-II.
* Generalize `coefficient` in `LivingTreeGene`.
* More tests (unit, integration, performance).
* Test app that visualizes the evolution process.
