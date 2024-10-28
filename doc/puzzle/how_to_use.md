# how to use?

这个在 os中是怎么用的:

这个地方 是一步一步进行封装的.

* 出题 所需要的参数
* 证明
* 广播

证明的地方是如何使用的.

方法的入口点就是 `prove` 以及 `get_proof_target` 本质上是一个 `prove`

```rust



impl<N: Network, C: ConsensusStorage<N>> Prover<N, C> {
    /// Performs one iteration of the puzzle.
    fn puzzle_iteration<R: Rng + CryptoRng>(
        &self,
        epoch_hash: N::BlockHash, // 块上数据
        coinbase_target: u64,     // 块上数据
        proof_target: u64,        // 块上数据
        rng: &mut R,              // 随机数
    ) -> Option<(u64, Solution<N>)> {
        // Increment the puzzle instances.
        // 证明之前 增加 puzzle 数量
        self.increment_puzzle_instances();

        debug!(
                "Proving 'Puzzle' for Epoch '{}' {}",
                fmt_id(epoch_hash),
                format!("(Coinbase Target {coinbase_target}, Proof Target {proof_target})").dimmed()
            );

        // Compute the solution.
        // 这个地方 其实也是 之前 OS 到 VM 中的 puzzle.prove
        // 注意它这个地方的使用方式
        let result =
            self.puzzle.prove(epoch_hash, self.address(), rng.gen(), Some(proof_target)).ok().and_then(|solution| {
                self.puzzle.get_proof_target(&solution).ok().map(|solution_target| (solution_target, solution))
            });

        // Decrement the puzzle instances.
        // 证明之后 减少数量
        self.decrement_puzzle_instances();
        // Return the result.
        result
    }
}

```



