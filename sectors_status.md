https://github.com/filecoin-project/lotus/blob/master/extern/storage-sealing/fsm.go
https://github.com/filecoin-project/lotus/blob/master/extern/storage-sealing/sector_state.go


# below is the status and step by step

```shell
switch state.State {
	// Happy path
	case Empty:
		fallthrough
	case WaitDeals:
		return m.handleWaitDeals, processed, nil
	case AddPiece:
		return m.handleAddPiece, processed, nil
	case Packing:
		return m.handlePacking, processed, nil
	case GetTicket:
		return m.handleGetTicket, processed, nil
	case PreCommit1:
		return m.handlePreCommit1, processed, nil
	case PreCommit2:
		return m.handlePreCommit2, processed, nil
	case PreCommitting:
		return m.handlePreCommitting, processed, nil
	case SubmitPreCommitBatch:
		return m.handleSubmitPreCommitBatch, processed, nil
	case PreCommitBatchWait:
		fallthrough
	case PreCommitWait:
		return m.handlePreCommitWait, processed, nil
	case WaitSeed:
		return m.handleWaitSeed, processed, nil
	case Committing:
		return m.handleCommitting, processed, nil
	case SubmitCommit:
		return m.handleSubmitCommit, processed, nil
	case SubmitCommitAggregate:
		return m.handleSubmitCommitAggregate, processed, nil
	case CommitAggregateWait:
		fallthrough
	case CommitWait:
		return m.handleCommitWait, processed, nil
	case CommitFinalize:
		fallthrough
	case FinalizeSector:
		return m.handleFinalizeSector, processed, nil

	// Handled failure modes
	case AddPieceFailed:
		return m.handleAddPieceFailed, processed, nil
	case SealPreCommit1Failed:
		return m.handleSealPrecommit1Failed, processed, nil
	case SealPreCommit2Failed:
		return m.handleSealPrecommit2Failed, processed, nil
	case PreCommitFailed:
		return m.handlePreCommitFailed, processed, nil
	case ComputeProofFailed:
		return m.handleComputeProofFailed, processed, nil
	case CommitFailed:
		return m.handleCommitFailed, processed, nil
	case CommitFinalizeFailed:
		fallthrough
	case FinalizeFailed:
		return m.handleFinalizeFailed, processed, nil
	case PackingFailed: // DEPRECATED: remove this for the next reset
		state.State = DealsExpired
		fallthrough
	case DealsExpired:
		return m.handleDealsExpired, processed, nil
	case RecoverDealIDs:
		return wrapCtx(m.HandleRecoverDealIDs), processed, nil

	// Post-seal
	case Proving:
		return m.handleProvingSector, processed, nil
	case Terminating:
		return m.handleTerminating, processed, nil
	case TerminateWait:
		return m.handleTerminateWait, processed, nil
	case TerminateFinality:
		return m.handleTerminateFinality, processed, nil
	case TerminateFailed:
		return m.handleTerminateFailed, processed, nil
	case Removing:
		return m.handleRemoving, processed, nil
	case Removed:
		return nil, processed, nil

	case RemoveFailed:
		return m.handleRemoveFailed, processed, nil

		// Faults
	case Faulty:
		return m.handleFaulty, processed, nil
	case FaultReported:
		return m.handleFaultReported, processed, nil

	// Fatal errors
	case UndefinedSectorState:
		log.Error("sector update with undefined state!")
	case FailedUnrecoverable:
		log.Errorf("sector %d failed unrecoverably", state.SectorNumber)
	default:
		log.Errorf("unexpected sector update state: %s", state.State)
	}

	return nil, processed, nil
}

```

on(SectorPreCommitted{}, PreCommitWait)---数据上链，扣除抵押



https://spec.filecoin.io/#section-algorithms.pos.post.windowpost



// The period over which a miner's active sectors are expected to be proven via WindowPoSt.
// This guarantees that (1) user data is proven daily, (2) user data is stored for 24h by a rational miner
// (due to Window PoSt cost assumption).
var WPoStProvingPeriod = abi.ChainEpoch(builtin.EpochsInDay) // 24 hours PARAM_SPEC

// The period between the opening and the closing of a WindowPoSt deadline in which the miner is expected to
// provide a Window PoSt proof.
// This provides a miner enough time to compute and propagate a Window PoSt proof.
var WPoStChallengeWindow = abi.ChainEpoch(30 * 60 / builtin.EpochDurationSeconds) // 30 minutes (48 per day) PARAM_SPEC

// WPoStDisputeWindow is the period after a challenge window ends during which
// PoSts submitted during that period may be disputed.
var WPoStDisputeWindow = 2 * ChainFinality // PARAM_SPEC

// The number of non-overlapping PoSt deadlines in a proving period.
// This spreads a miner's Window PoSt work across a proving period.
const WPoStPeriodDeadlines = uint64(48) // PARAM_SPEC

// MaxPartitionsPerDeadline is the maximum number of partitions that will be assigned to a deadline.
// For a minimum storage of upto 1Eib, we need 300 partitions per deadline.
// 48 * 32GiB * 2349 * 300 = 1.00808144 EiB
// So, to support upto 10Eib storage, we set this to 3000.


**Network parameters**:

- Supported Sector Sizes: `32 GiB` and `64 GiB`
- Consensus Miner Min Power: `32 GiB`
- Epoch Duration Seconds: `30`
- Expected Leaders per Epoch: `5` 
- WindowPoSt Proving Period: `2880`
- WindowPoSt Challenge Window: `60`
- WindowPoSt Period Deadlines: `48`
- Pre-Commit Challenge Delay: `150


WindowPoSt Proving Period: `2880`
WindowPoSt Challenge Window: `60`


