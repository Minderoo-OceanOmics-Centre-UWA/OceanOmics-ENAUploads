# Umbrella project description update (2026-10-30)

Replaces the `<DESCRIPTION>` of the umbrella project PRJEB63306 with the current
OceanOmics Centre acknowledgement and data-use text.

The `<TITLE>` also changes, from `Environmental DNA` to `Environmental DNA (eDNA)`.
This was not optional: ENA's XML schema enforces `minLength` 20 on `TITLE` in
`ProjectType`, and the live title was 17 characters, so a MODIFY carrying it was
rejected with

    string length (17) is less than minLength facet (20) for type of TITLE element

The short title was presumably set through the Webin Portal, which does not
enforce the XML schema. Any future XML update of this project faces the same
constraint. Nothing else about the project is changed.

## Run

    curl -u 'WEBIN_ACCOUNT':'PASSWORD' \
      -F "SUBMISSION=@update.xml" \
      -F "PROJECT=@umbrella_modified.xml" \
      "https://www.ebi.ac.uk/ena/submit/drop-box/submit/" > RESULTS.xml

Replace `WEBIN_ACCOUNT` and `PASSWORD` with the Webin submission account that
owns PRJEB63306. The project is already PUBLIC, so the submission uses `MODIFY`
only, with no `RELEASE` action.

Check that `RESULTS.xml` comes back with `success="true"`. If it reports
`The XML md5 checksum for the object being updated has not changed`, the
submission was accepted but changed nothing.

## Important: MODIFY replaces the entire project record

ENA does not patch individual fields. Whatever is in `umbrella_modified.xml`
becomes the project, so any element left out is removed. The child project list
in particular must be complete, or children silently disappear from the
umbrella.

For this reason `umbrella_modified.xml` was **not** derived from the older files
in this repo, which had drifted badly from the live record: they still carried
the 2023 title, the old centre name, and only 7 of the 32 child projects. It was
instead rebuilt from the live record, retaining the centre name
`Minderoo OceanOmics Centre at UWA` and all 32 children, with the description
swapped and the title lengthened as described above.

Re-fetch the live record and reconcile the child list immediately before
submitting, since children are added by other pipelines:

    curl -s "https://www.ebi.ac.uk/ena/browser/api/xml/PRJEB63306"

The child list in `umbrella_modified.xml` was verified identical to the live
record on 2026-10-30. Note that the live record had last been updated on
2026-10-18 by a submission made outside this repo.

## Note on the description text

`Data Use & Sharing Policy` is stored as `Data Use &amp; Sharing Policy`, since
a bare `&` is not legal XML. It displays as a plain `&`.

The text is held as three paragraphs separated by newlines. ENA's browser tends
to render a description as a single run-on block, so the paragraph breaks may not
be visible once published.
