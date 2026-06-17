---
type: project
status: idea         # idea | building | shipped
repo: 
blog: 
tags: []
created: 2026-06-15
---

# Flux Snapshot

## Goal

## Algorithm(s)
- [[ ]]

## Extension point (controller / scheduler / CNI / CRD)

## Concepts & patterns to reuse
- 

## Log

problems: 
- traversing the flux dependency graph -> recursive function
- each ks in a ks chain will emit a fail condition -> deduplicate window + lastTimeSeen field + thread safe counter 
- identifiing types using group,version,kind
- 