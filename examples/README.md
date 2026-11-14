# Worked examples

The results of the appendix "Worked Examples" of the document, and the maps that the appendix says are rejected.

- `NAME.edn`: a result in CBOR diagnostic notation. Everything else comes from it.
- `NAME.hex`: the encoding of the result, derived by `make`.
- `NAME.pretty`: the encoding annotated for the figure of the appendix. It is written by hand, and `make` checks that it holds the same bytes.
- `rejected/NAME.edn`: a map that the CDDL of the document must reject.

The document includes the `.edn`, `.hex` and `.pretty` files.

`make` derives the encodings and then checks everything against `../cddl/geographic-results.cddl`.
`make check` only checks.
The top of the `Makefile` lists the checks and the tools.

To change a result, edit its `.edn` file and run `make`.
If `make` then reports the `.pretty` file, bring that file in line by hand.

## Origin

`azure-west-europe` and `google-europe-west4` are two of the 19 results of the Verifier run that the appendix describes.
`result-on-result` is the result of the second Verifier of the chain over the TRIP trajectory.
The Verifier, the artifacts it ran over and the signed EARs are at <https://github.com/nikolaichuk7/geoar-verifier>.
