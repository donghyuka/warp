# Smart-seq2 Multi Sample Overview

| Pipeline Version | Date Updated | Documentation Author | Questions or Feedback |
| :----: | :---: | :----: | :--------------: |
| [MultiSampleSmartSeq2_v2.1.4](https://github.com/broadinstitute/warp/releases) | November, 2020 | [Elizabeth Kiernan](mailto:ekiernan@broadinstitute.org) | Please file GitHub issues in WARP or contact [Kylee Degatano](mailto:kdegatano@broadinstitute.org) |

## Introduction

The Smart-seq2 Multi Sample pipeline is a wrapper around the [Smart-seq2 Single Sample](../Smart-seq2_Single_Sample_Pipeline/) pipeline. It is developed by the Data Coordination Platform of the Human Cell Atlas to process single-cell RNAseq (scRNAseq) data generated by Smart-seq2 assays. The workflow processes multiple cells by importing and running the [Smart-seq2 Single Sample workflow](https://github.com/broadinstitute/warp/blob/master/pipelines/skylab/smartseq2_single_sample/SmartSeq2SingleSample.wdl) for each individual cell and then merging the resulting Loom output files into a single Loom file.

Full details about the Smart-seq2 pipeline can be read in the [Smart-seq2 Single Sample documentation](../Smart-seq2_Single_Sample_Pipeline/) in GitHub.

The Multi Sample Smart-seq2 workflow can also be run in [Terra](https://app.terra.bio), a cloud-based analysis platform. The Terra [Smart-seq2 public workspace](https://app.terra.bio/#workspaces/featured-workspaces-hca/HCA%20Smart-seq2%20Multi%20Sample%20Pipeline) contains the Smart-seq2 workflow, workflow configurations, required reference data and other inputs, and example testing data.

:::tip Want to use the Smart-seq2 Multi Sample pipeline for your publication?
Check out the [Smart-seq2 Publication Methods](./smart-seq2.methods.md) to get started!
:::

## Inputs

There are two example configuration (JSON) files available for testing the Multi Sample workflow. Both examples are also preloaded in the Terra [Smart-seq2 public workspace](https://app.terra.bio/#workspaces/featured-workspaces-hca/HCA%20Smart-seq2%20Multi%20Sample%20Pipeline).
* [human_single_example.json](https://github.com/broadinstitute/warp/blob/master/pipelines/skylab/smartseq2_multisample/human_single_example.json): Configurations for an example single-end human dataset consisting of two samples (cells)
* [mouse_paired_example.json](https://github.com/broadinstitute/warp/blob/master/pipelines/skylab/smartseq2_multisample/mouse_paired_example.json): Configurations for an example paired-end mouse dataset consisting of two samples (cells)


### Sample Data Input

The sample data inputs are similar to those specified in the [Smart-seq2 Single Sample README](https://github.com/broadinstitute/warp/blob/master/pipelines/skylab/smartseq2_single_sample/README.md). The Smart-seq2 Multi Sample workflow processes both single- and paired-end samples; however, these samples can not be mixed in the same run.

For the Multi Sample workflow, FASTQ files must be located in a cloud-accessible folder.


| Input name | Input Description | Input Type |
| --- | --- | --- |
| fastq1_input_files | Cloud locations for each read1 file | Array of strings | 
| fastq2_input_files | Optional cloud locations for each read2 file if running paired-end samples |Array of strings |
| input_ids | Unique identifiers or names for each cell; can be a UUID or human-readable name | Array of strings |
| input_names | Optional unique identifiers/names to further describe each cell. If `input_id` is a UUID, the `input_name` could be used as a human-readable identifier | String |
| batch_id | Identifier for the batch of multiple samples | String |
| batch_name | Optional string to describe the batch or biological sample | String |
| input_name_metadata_field | Optional input describing, when applicable, the metadata field containing the `input_names` | String |
| input_id_metadata_field | Optional string describing, when applicable, the metadata field containing the `input_ids` | String |



### Additional Input

The reference inputs are identical to those specified in the "Additional Reference Inputs" section of the [Smart-seq2 Single Sample README](https://github.com/broadinstitute/warp/blob/master/pipelines/skylab/smartseq2_single_sample/README.md).


### Smart-seq2 Multi Sample Task Summary

The Smart-seq2 Multi Sample pipeline calls three tasks:

1) [SmartSeq2SingleSample.wdl](https://github.com/broadinstitute/warp/blob/master/pipelines/skylab/smartseq2_single_sample/SmartSeq2SingleSample.wdl): a task that runs the Smart-seq2 Single Sample workflow
2) [SmartSeq2PlateAggregation.wdl](https://github.com/broadinstitute/warp/blob/master/tasks/SmartSeq2PlateAggregation.wdl): the wrapper pipeline that checks the inputs, initiates per cell processing and aggregates the results
3) [LoomUtils.wdl](https://github.com/broadinstitute/warp/blob/master/tasks/LoomUtils.wdl): a task that produces the Loom file output


### Outputs

The following table lists the outputs of the Smart-seq2 Multi Sample workflow.

| Output file name | Output Description | Output Type |
| --- | --- | --- |
| bam_files | An array of genome-aligned BAM files (one for each sample) generated with HISAT2  | Array |
| bam_index_files |  An array of BAM index files generated with HISAT2 | Array |
| loom_output | A single Loom file  | File |

The Loom file is an aggregate of all the individual Loom files generated using the [Smart-seq2 Single Sample workflow](https://github.com/broadinstitute/warp/blob/master/pipelines/skylab/smartseq2_single_sample/SmartSeq2SingleSample.wdl). The aggregated Loom filename contains the `plateid` prefix, which is set by default to the `batch_id` string specified in the input. The `plateid` is also set as a global attribute in the Loom.

Both the individual sample Loom files and individual BAM files are described in the [Smart-seq2 Single Sample README](../Smart-seq2_Single_Sample_Pipeline/).

:::warning Zarr Array Deprecation Notice June 2020
Please note that we have deprecated the previously used Zarr array output. The pipeline now uses the Loom file format as the default output.
:::

## Versioning

Release information for the Smart-seq2 Multi Sample Pipeline can be found in the [changelog](https://github.com/broadinstitute/warp/blob/master/pipelines/skylab/smartseq2_multisample/MultiSampleSmartSeq2.changelog.md). Please note that any major changes to the Smart-seq2 pipeline will be documented in the [Smart-seq2 Single Sample changelog](https://github.com/broadinstitute/warp/blob/master/pipelines/skylab/smartseq2_single_sample/SmartSeq2SingleSample.changelog.md).