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
