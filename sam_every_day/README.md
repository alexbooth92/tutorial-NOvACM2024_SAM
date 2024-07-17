First, log onto a GPVM and set up your environment.
```
setup_nova
spack load nova-grid-utils
sl7-nova -r development
```

We interact with SAM's file catalogue via `samweb`. To see all available commands:
```
samweb --help-commands
```

An example of a query to find files for making ND event displays for a particular run and subrun in data. 
```
samweb list-files "data_tier pid and nova.detectorid nd and online.stream 0 and file_type importedDetector and Online.RunNumber 14021 and Online.Subrun 8"
```

A file's location can be found using the `locate-file` command.
```
samweb locate-file neardet_r00014021_s08_t00_R20-11-25-prod5.1reco.u_v1_data.pid.root
```

A file's complete metadata can be viewed using the `get-metadata` command.
```
samweb get-metadata neardet_r00014021_s08_t00_R20-11-25-prod5.1reco.u_v1_data.pid.root
```

Can look at all of the definition names that exist using `list-definitions` (there are lots if you don't cut on, for example, user).
```
samweb list-definitions --user=abooth --after=2024-07-13
```

Can look at the metadata query a definition name labels using `describe-definition`.
```
samweb describe-definition abooth_240714_tutorial
```

Can count the number of files in a definition using `count-definition-files`.
```
samweb count-definition-files abooth_240714_tutorial
```

Can list the files in a definition using `list-definition-files`.
```
samweb list-definintion-files abooth_240714_tutorial
```

An example of a `base` definition.
```
samweb describe-definition base_fd_mc_full
```

Can count the number of files satisfying a particular query.
```
samweb count-files "defname: abooth_240714_tutorial with stride 5 limit 10"
```

Once happy with the query you can create your own definition using `create-definition`. The name should be as descriptive as possible.
```
samweb create-definition "$USER"_"$(date '+%y%m%d')"_tutorial_stride5_limit10 "defname: abooth_240714_tutorial with stride 5 limit 10"
```

A definition snapshot represent the output of a given query at a fixed point in time. You can create such a snapshot using the `take-snapshot` command.
```
samweb take-snapshot abooth_240714_tutorial
```

The information about a particular snapshot can be viewed using `snapshot-info`.
```
samweb snapshot-info --defname=abooth_240714_tutorial
```

There is a tool for making a definition from a file list of arbitrary length.
```
python $NOVAPRODUCTION_DIR/scripts/make_definition_from_file_list.py --help
```

The following commands can be used to make a file list that can be referred to from grid jobs to avoid querying SAM directly and overloading it.
```
cache_state.py -d abooth_240714_tutorial --list-cached --xrootd
mv cached.list /pnfs/nova/persistent/analysis/sam_cache_user/$USER/abooth_240714_tutorial.list
```

To have your grid jobs use the file list you made in the step above, you must export the location of the file list to your grid jobs via the `--export` option of `submit_cafana.py`.
```
submit_cafana.py --export CAFANA_DATASET_CACHE_DIR=/cvmfs/nova.osgstorage.org/analysis/sam_cache_user ......
```

`file-lineage` is a tool for identifying the file(s) that a particular file was made from (parent) or went onto make (child).
```
samweb file-lineage ancestors fardet_genie_AR23_20i_00_000_nonswap_genierw_fhc_v08_1000_r00013589_s44_c000_R23-11-16-miniprod6.1reco.a_v1_20231218_105527_sim_miniprod6-1.caf.root
```

An important example of using lineage is with the concats.
```
samweb file-lineage parents samweb file-lineage parents prod_sumdecaf_R20-11-25-prod5.1reco.j.l_fd_genie_N1810j0211a_nonswap_rhc_nova_v08_full_v1_numu2020_149_of_150.root
```

A standard user can declare their own files to SAM and create corresponding definitions using `sam4users`.
```
sam_add_dataset -d /pnfs/nova/persistent/users/$USER/WC_240715 -n abooth_240717_alister_tutorial_g4
```

After `sam4users` definitions are made, you can interact with them via `samweb` in the usual way.
```
samweb describe-definition abooth_240717_alister_tutorial_g4
```
