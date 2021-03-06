- **5/9**: Per Y's suggestion I add a term to the loss function asking the frame-level RNN to predict the next frame, without help of the sample-level MLP.
	- Before: `twotier_determ_bigrun_qzero_1462749482`, 1.827 iters 0-10K, 1.513 iters 90K-100K. (copied from below)
	- After: `twotier_ipcost_1462871075`
	- I also try weighting the auxiliary cost term by 0.1: `twotier_ipcost_weighted_1462891119`
- **5/9**: I try changing my input normalization so that samples have zero DC offset (per Kyle McDonald's suggestion). Unfortunately this is probably going to improve NLL, but in a way that's meaningless. I'll evaluate by listening to samples and checking them in Audacity.
	- `twotier_zero_dc_offset_1462873780`
- **5/9**: I implement a flat, non-heirarchical baseline model (`baseline.py`) and evaluate it against two-tier. I try two variants, feeding values into the GRUs as real values (what I did in two-tier) and as embeddings. I report NLLs in bits per sample on the train set (this is mostly OK because I never make it through one epoch). **TODO report results**
	- Controlling for wall-clock time, where each model uses its own reasonable hyperparams (to see which model "wins" overall):
		- Two-tier: `twotier_time_benchmark_1462865129`
		- Flat reals seqlen 64: `speech_baseline_time_reals_seqlen64_1462866948`
		- Flat reals seqlen 128: `speech_baseline_time_reals_seqlen128_1462867000`
		- Flat embeddings seqlen 128: `speech_baseline_time_embed_seqlen64_1462867483`
		- Flat embeddings seqlen 64: `speech_baseline_time_embed_seqlen128_1462867499`
	- Controlling for number of training steps, where each step has the same SEQ_LEN (to see if the two-tier model's gains might just be by virtue of training speed):
		- Two-tier: `twotier_determ_bigrun_qzero_1462749482`, 1.827 iters 0-10K, 1.513 iters 90K-100K. ***best model so far***
		- Flat reals: `speech_baseline_iters_reals_1462866911`
		- Flat embeddings: `speech_baseline_iters_embed_1462867526`
- **5/8**: To better understand how the model uses its softmax output, I sample from a 1024-dim model trained for 50K iterations and plot the softmax output distribution at each timestep. See `notes/softmax_visualization.mp4` (action starts around 7:00). I find the model learns roughly-Gaussian unimodal distributions.
- **5/8**: I'm worried that the samples don't sound quite as good as the old implementation for some reason, so I make the script deterministic (`numpy.random.seed(123)`) and carefully step through the entire model, making sure its generated samples matched my previous implementation number-for-number.
- **5/7**: Initial release of a cleaned-up (actually mostly rewritten) version of my current best model in `two_tier.py`. Written description in `notes/two_tier.txt` and hastily-drawn model diagram in `notes/two_tier.jpg`.