Compile inference routines
Go to Project/codes/LDhat-modifiedIntegrated/
$ make -f makefile_cpp
Go to Project/codes/LDhat-modifiedStat/
$ make
Go to Project/codes/crossing/
$ make

Compile Nanopore_sequencing_simulation codes and error_correction codes:
Go to Project/sim_data/scripts_sim_nanoerr_corr/
$ make clean
$ make all
Go to Project/sim_data/scripts_sim_nanolen/
$ make clean
$ make
Go to Project/sim_data/scripts_make_aligned_or_not/
$ make clean
$ make all
Go to Project/sim_data/scripts_plugin_N/
$ make clean
$ make all
Go to Project/sim_data/scripts_select_seq_with_more_than_x_seg_sites/
$ make clean
$ make all



SET_UP
####################################################################

To simulate Nanopore sequecing N individuals sampled from a large random mating population (N is an interger determined by user, and here we use N=100 as an example):
Set up scripts repository in Project/
>>>
Go to Project/:
$ rm -r scripts_simulations_2w_N100/ 
$ rm -r sim_data/simulation_data_match_N100/		#clean up if necessary
$ cp -r scripts_simulations_2w_stem_coppies/ scripts_simulations_2w_N100/
Go to Project/scripts_simulations_2w_N100/
$ source generate-sim-samples-match-2way.sh 100		#('100' here is an interger determined by user, and here we use N=100 as an example)

#Go to Project/sim_data/scripts_sim_nanolen/
$ ./sim_nanolen <seed> 0 ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.true_nanolen	#<seed> user defined random interger

#Go to Project/sim_data/scripts_make_aligned_or_not/ 
$ ./make_not_aligned ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.true_nanolen ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.true_nanolen.not_aligned

#Go to Project/sim_data/scripts_sim_nanoerr_corr/
$ ./input_error <seed> 0 ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen.not_aligned ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.true_nanolen.not_aligned	#<seed> user defined random interger #this step put errors in the sequences

Now Project/sim_data/simulation_data_match_N100/ contains the simulated raw Nanopore sequencing file, which is "input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen.not_aligned". In this file, first line is the LDhat header, '>' denotes the sequence number, '#' denotes marker position(relative rankings within markers), lines with '1', '0' and/or 'N' denotes genotypes at coeesponding marker position



SIM_1 No correction
#####################################################################

Run simulated Nanopore sequencing data, without correction, set inclusion criterion at 25 informative markers per sequence

#Go to Project/sim_data/scripts_make_aligned_or_not/
$ ./make_aligned ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen.not_aligned ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen.aligned

#Go to Project/sim_data/scripts_select_seq_with_more_than_x_seg_sites/
$ ./select_seqs 25 ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen.aligned ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen	#first argument (here 25) is an user defined interger denote the inclusion criterion

#Go to Project/sim_data/
$ source gen-LK-file.sh 	#generate specific LK file for the remaining sequence count

#Go to Project/scripts_simulations_2w_N100/
$ .customise.sh
$ ./generate_multiple_scripts.sh

Each example-inference-sim-2way.i.sh (i in 1:100) runs 5 bootstraps, to run 1 set:
$ ./example-inference-sim-2way.i.sh (i in 1:100)
When there is computing resource, the 100 sets can be run simultaneously.
Results will be stored in Project/inference_results_sim_example_N100/





SIM_2 "revert_consecutive_flips" Method correction
###############################################################

Run simulated Nanopore sequencing data, with "revert_consecutive_flips" method correction, set inclusion criterion at 25 informative markers per sequence

Go back to SET_UP and repeat after >>>

#Go to #Go to Project/sim_data/scripts_sim_nanoerr_corr/
$ ./corr_singleflips ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen.not_aligned ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen_corrflips.not_aligned

#Go to Project/sim_data/scripts_make_aligned_or_not/
$ ./make_aligned ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen_corrflips.not_aligned ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen_corrflips.aligned

#Go to Project/sim_data/scripts_select_seq_with_more_than_x_seg_sites/
$ ./select_seqs 25 ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen_corrflips.aligned ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen	#first argument (here 25) is an user defined interger denote the inclusion criterion

#Go to Project/sim_data/
$ source gen-LK-file.sh 	#generate specific LK file for the remaining sequence count

#Go to Project/scripts_simulations_2w_N100/
$ .customise.sh
$ ./generate_multiple_scripts.sh

Each example-inference-sim-2way.i.sh (i in 1:100) runs 5 bootstraps, to run 1 set:
$ ./example-inference-sim-2way.i.sh (i in 1:100)
When there is computing resource, the 100 sets can be run simultaneously.
Results will be stored in Project/inference_results_sim_example_N100/





SIM_3 "plug_in_N" Method correction
###############################################################

Run simulated Nanopore sequencing data, with "plug_in_N" method correction, here "plugin_N" threshold sets at Phredscore=5 as an example. Set inclusion criterion at 25 informative markers per sequence.

Go back to SET_UP and repeat after >>>

#Go to Project/sim_data/scripts_plugin_N/
$ ./plugin_N_not_aligned 5 ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen.not_aligned ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen_corr5N.not_aligned 	#first argument(here example is 5) is a user defined interger (>0) that sets the Phredscore filtering threshold, and any base read that scored lower than this threshold will be classified as 'missing'. 

#Go to Project/sim_data/scripts_make_aligned_or_not/
$ ./make_aligned ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen_corr5N.not_aligned ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen_corr5N.aligned

#Go to Project/sim_data/scripts_select_seq_with_more_than_x_seg_sites/
$ ./select_seqs 25 ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen.nanoerr_nanolen_corr5N.aligned ../simulation_data_match_N100/input_profile_mean_chr3_2way.txt.N1.0e5.6330.sample.1.Ns100.12gen	#first argument (here 25) is an user defined interger denote the inclusion criterion

#Go to Project/sim_data/
$ source gen-LK-file.sh 	#generate specific LK file for the remaining sequence count

#Go to Project/scripts_simulations_2w_N100/
$ .customise.sh
$ ./generate_multiple_scripts.sh

Each example-inference-sim-2way.i.sh (i in 1:100) runs 5 bootstraps, to run 1 set:
$ ./example-inference-sim-2way.i.sh (i in 1:100)
When there is computing resource, the 100 sets can be run simultaneously.
Results will be stored in Project/inference_results_sim_example_N100/

