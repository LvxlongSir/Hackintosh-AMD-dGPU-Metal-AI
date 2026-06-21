# Hackintosh-AMD-dGPU-Metal-AI
Yes, Verified successfully
<img width="1639" height="620" alt="image" src="https://github.com/user-attachments/assets/ce7d1f9f-d3ff-46f0-b0ed-4996fa2aaff7" />


#Pathway llama+GGML (others pls see following md docs)
	Prefered macOS 15 (26 n't unlock airdrop with brocm), coffee/tea, midnight, mind, hand...

AMD dGPU support Metal (mine is Radeon RX 6900XT)

	a4@iMac ~ % system_profiler SPDisplaysDataType | grep -i metal
	      Metal Support: Metal 3

llama.cpp to build for Metal backend, note to tn-off Flash-attn

	rm -rf build && \
	cmake -B build \
	  -DGGML_METAL=ON \
	  -DGGML_METAL_EMBED_LIBRARY=ON \
	  -DGGML_FLASH_ATTN=OFF \
	  -DGGML_ACCELERATE=ON \
	  -DGGML_NATIVE=ON \
	  -DCMAKE_BUILD_TYPE=Release && \
	cmake --build build --config Release -j$(sysctl -n hw.logicalcpu) && \
	sudo cmake --install build --config Release && \
	for f in /usr/local/bin/llama-*; do  \
	  sudo install_name_tool -delete_rpath /usr/local/lib "$f" 2>/dev/null || true && \
	  sudo install_name_tool -add_rpath /usr/local/lib "$f"; \
	done && \
	llama-cli --version

	a4@iMac ~ % llama-cli --version
	version: 9618 (c34b92235)
	built with AppleClang 17.0.0.17000604 for Darwin x86_64

Verify 

	a4@iMac ~ % llama-cli --list-devices                                           
	Available devices:
	  MTL0: AMD Radeon RX 6900 XT (16368 MiB, 16367 MiB free)
	  BLAS: Accelerate (0 MiB, 0 MiB free)

VVVery IMPORTANT for AMD dGPU

	GGML_METAL_CONCURRENCY_DISABLE=1
	othwise n't work
	e.g. export GGML_METAL_CONCURRENCY_DISABLE=1

	below to utilize perf
	export GGML_METAL_VRAM_RESERVE_MB=512
	export GGML_METAL_FORCE_PRIVATE=1
	export GGML_METAL_N_CB=2

Serve

	llama-server \
	-m /Volumes/SSD/ModelCache/Qwen3.6-35B-A3B-Uncensored-HauhauCS-Aggressive-IQ4_NL.gguf \
	--host 127.0.0.1 \
	--port 8080 \
	-ngl 99 -c 4096 -b 512 \
	--port 8080 --jinja  

Webbrower/llama-ui(Chrome app)
	
	http://127.0.0.1:8080/

	Say sth, "Kindness multiplies when shared" 
	<img width="777" height="112" alt="image" src="https://github.com/user-attachments/assets/3ba55682-cd31-41f0-b043-a1d87685c0cf" />
	<img width="970" height="830" alt="image" src="https://github.com/user-attachments/assets/fcdcb20b-bfa5-4fd6-9e63-3a6ce9f2b6be" />




	
