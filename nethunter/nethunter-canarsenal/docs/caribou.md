---
title: 넷헌터 CARsenal : Caribou
description: 사용자 친화적인 자동차 보안 탐색 도구.
icon:
weight:
author: ["v0lk3n",]
번역: ["kmw0410"]
---

<p style="text-align: center"><img src="../assets/caribou.gif" width="350" alt="CARsenal Caring Caribou"></p>

> 모듈과 하위 모듈을 선택하면 설정 필드에 해당 매개변수가 표시돼요.

### 모듈과 하위 모듈

- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/dump.md" target="_blank">Dump</a>
- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/fuzzer.md" target="_blank">Fuzzer</a>
    <details>
	    <summary>brute</summary>
			  ```bash
			  usage: caringcaribou fuzzer brute [-h] [-file FILE] [-responses] [-index I] [-delay D] arb_id data  
  
			  positional arguments:  
			   arb_id          arbitration ID  
			   data            hex data where dots mark indices to bruteforce, e.g. 123.AB..  
  
			 options:  
			  -h, --help      show this help message and exit  
			  -file, -f FILE  log file for cansend directives  
			  -responses, -r  print responses to stdout  
			  -index, -i I    start index (for resuming previous session)  
			  -delay D        delay between messages
			  ```
	</details>
    <details>
	    <summary>identify</summary>
			  ```bash
			  usage: caringcaribou fuzzer identify [-h] [-responses] [-delay D] filename  
  
			  positional arguments:  
			   filename          input directive file to replay  
  
			 options:  
			  -h, --help        show this help message and exit  
			  -responses, -res  print responses to stdout  
			  -delay D          delay between messages
			  ```
	</details>	
    <details>
	    <summary>mutate</summary>
			 ```bash
			 usage: caringcaribou fuzzer mutate [-h] [-responses] [-file FILE] [-seed S] [-index I] [-delay D] arb_id data  
  
			 positional arguments:  
			  arb_id          hex arbitration ID where dots mark indices to mutate, e.g. 7f..  
			  data            hex data where dots mark indices to mutate, e.g. 123.AB..  
  
			 options:  
			  -h, --help      show this help message and exit  
			  -responses, -r  print responses to stdout  
			  -file, -f FILE  log file for cansend directives  
			  -seed, -s S     set random seed  
			  -index, -i I    start index (for resuming previous session)  
			  -delay D        delay between messages
			  ```
	</details>
    <details>
	    <summary>random</summary>
			```bash
			 usage: caringcaribou fuzzer random [-h] [-id ID] [-data DATA] [-file FILE] [-min MIN] [-max MAX] [-index I]  [-seed S] [-delay D]  
  
			 options:  
			  -h, --help      show this help message and exit  
			  -id ID          set static arbitration ID  
			  -data, -d DATA  set static data  
			  -file, -f FILE  log file for cansend directives  
			  -min MIN        minimum data length  
			  -max MAX        maximum data length  
			  -index, -i I    start index (for resuming previous session)  
			  -seed, -s S     set random seed  
			  -delay D        delay between messages
			 ```
	</details>		
    <details>
	    <summary>replay</summary>
			  ```bash
			  usage: caringcaribou fuzzer replay [-h] [-requests] [-responses] [-delay D] filename  
  
			 positional arguments:  
			  filename          input directive file to replay  
  
			 options:  
			  -h, --help        show this help message and exit  
			  -requests, -req   print requests to stdout  
			  -responses, -res  print responses to stdout  
			  -delay D          delay between messages
			  ```
	</details>					
- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/listener.md" target="_blank">Listener</a>
<details>
	   <summary>module_template</summary>
		  ```bash
		 usage: caringcaribou module_template [-h] [-id ID]  
  
		 Descriptive message for the template module  
  
		 options:  
		  -h, --help  show this help message and exit  
		  -id ID      arbitration ID to use  
  
		 Example usage:  
		  caringcaribou module_template  
		  caringcaribou module_template -id 123  
		  caringcaribou module_template -id 0x1FF
		  ```
</details>	
- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/send.md" target="_blank">Send</a>
    <details>
	    <summary>file</summary>
			  ```bash		    
			 usage: caringcaribou send file [-h] [--delay D] [--loop] filename  
  
			 positional arguments:  
			  filename       path to file  
  
			 options:  
			  -h, --help     show this help message and exit  
			  --delay, -d D  delay between messages in seconds (overrides timestamps in file)  
			  --loop, -l     loop message sequence (re-send over and over)
			  ```
	</details>
    <details>
	    <summary>message</summary>
			  ```bash  
			 usage: caringcaribou send message [-h] [--delay D] [--loop] [--pad] msg [msg ...]  
  
			 positional arguments:  
			  msg            message on format ARB_ID#DATA where ARB_ID is interpreted as hex if it starts with 0x and decimal otherwise. DATA consists of 1-8 bytes written in hex and separated by dots.  
  
			 options:  
			  -h, --help     show this help message and exit  
			  --delay, -d D  delay between messages in seconds  
			  --loop, -l     loop message sequence (re-send over and over)  
			  --pad, -p      automatically pad messages to 8 bytes length
			  ```
	</details>	 		 
- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/uds.md" target="_blank">UDS</a>
    <details>
	    <summary>discovery</summary>
			  ```bash  
			  usage: caringcaribou uds discovery [-h] [-min MIN] [-max MAX] [-b B [B ...]] [-ab N] [-sv] [-d D]  
  
			 options:  
			  -h, --help            show this help message and exit  
			  -min MIN              min arbitration ID to send request for  
			  -max MAX              max arbitration ID to send request for  
			  -b, --blacklist B [B ...] arbitration IDs to blacklist responses from  
			  -ab, --autoblacklist N listen for false positives for N seconds and blacklist matching arbitration IDs before running discovery  
			  -sv, --skipverify     skip verification step (reduces result accuracy)  
			  -d, --delay D         D seconds delay between messages (default: 0.01)
			  ```
	</details>
    <details>
	    <summary>services</summary>
			  ```bash  
			  usage: caringcaribou uds services [-h] [-t T] src dst  
  
			 positional arguments:  
			  src              arbitration ID to transmit to  
			  dst              arbitration ID to listen to  
  
			 options:  
			  -h, --help       show this help message and exit  
			  -t, --timeout T  wait T seconds for response before timeout (default: 0.2)
			  ```
	</details>
    <details>
	    <summary>subservices</summary>
			  ```bash  
			  usage: caringcaribou uds subservices [-h] [-t T] dtype stype src dst  
  
			 positional arguments:  
			  dtype            Diagnostic Session Control Subsession Byte  
			  stype            Service ID  
			  src              arbitration ID to transmit to  
			  dst              arbitration ID to listen to  
  
			 options:  
			  -h, --help       show this help message and exit  
			  -t, --timeout T  wait T seconds for response before timeout (default: 0.02)
			  ```
	</details>
    <details>
	    <summary>ecu_reset</summary>
			  ```bash  			    
			 usage: caringcaribou uds ecu_reset [-h] [-t T] type src dst  
  
			 positional arguments:  
			  type             Reset type: 1=hard, 2=key off/on, 3=soft, 4=enable rapid power shutdown, 5=disable rapid power shutdown  
			  src              arbitration ID to transmit to  
			  dst              arbitration ID to listen to  
  
			 options:  
			  -h, --help       show this help message and exit  
			  -t, --timeout T  wait T seconds for response before timeout
			  ```
	</details>
    <details>
	    <summary>testerpresent</summary>
			  ```bash  			    
			 usage: caringcaribou uds testerpresent [-h] [-d D] [-dur S] [-spr] src  
  
			 positional arguments:  
			  src                 arbitration ID to transmit to  
  
			 options:  
			  -h, --help          show this help message and exit  
			  -d, --delay D       send TesterPresent every D seconds (default: 0.5)  
			  -dur, --duration S  automatically stop after S seconds  
			  -spr                suppress positive response
			  ```
	</details>
    <details>
	    <summary>security_seed</summary>
			  ```bash  
			  usage: caringcaribou uds security_seed [-h] [-r RTYPE] [-d D] [-n NUM] stype level src dst  
  
			 positional arguments:  
			  stype              Session Type: 1=defaultSession 2=programmingSession 3=extendedSession 4=safetySession [0x40-0x5F]=OEM [0x60-0x7E]=Supplier [0x0, 0x5-0x3F, 0x7F]=ISOSAEReserved  
			  level              Security level: [0x1-0x41 (odd only)]=OEM 0x5F=EOLPyrotechnics [0x61-0x7E]=Supplier [0x0, 0x43-0x5E, 0x7F]=ISOSAEReserved  
			  src                arbitration ID to transmit to  
			  dst                arbitration ID to listen to  
  
			 options:  
			  -h, --help         show this help message and exit  
			  -r, --reset RTYPE  Enable reset between security seed requests. Valid RTYPE integers are: 1=hardReset, 2=key off/on, 3=softReset, 4=enable rapid power shutdown, 5=disable rapid power shutdown. (default: None)  
			  -d, --delay D      Wait D seconds between reset and security seed request. You'll likely need to increase this when using RTYPE: 1=hardReset. Does nothing if RTYPE is None. (default: 0.01)  
			  -n, --num NUM      Specify a positive number of security seeds to capture before terminating. A '0' is interpreted as infinity. (default: 0)
			  ```
	</details>
    <details>
	    <summary>dump_dids</summary>
			  ```bash  		    
			 usage: caringcaribou uds dump_dids [-h] [-t T] [--min_did MIN_DID] [--max_did MAX_DID] src dst  
  
			 positional arguments:  
			  src                arbitration ID to transmit to  
			  dst                arbitration ID to listen to  
  
			 options:  
			  -h, --help         show this help message and exit  
			  -t, --timeout T    wait T seconds for response before timeout  
			  --min_did MIN_DID  minimum device identifier (DID) to read (default: 0x0000) 
			  --max_did MAX_DID  maximum device identifier (DID) to read (default: 0xFFFF)
			  ```
	</details>
    <details>
	    <summary>read_mem</summary>
			  ```bash  
			  usage: caringcaribou uds read_mem [-h] [-t T] [--start_addr START_ADDR] [--mem_length MEM_LENGTH] [--mem_size MEM_SIZE] [--address_byte_size ADDRESS_BYTE_SIZE] [--memory_length_byte_size MEMORY_LENGTH_BYTE_SIZE] [--outfile OUTFILE] src dst  
  
			 positional arguments:  
			  src                   arbitration ID to transmit to  
			  dst                   arbitration ID to listen to  
  
			 options:  
			  -h, --help            show this help message and exit  
			  -t, --timeout T       wait T seconds for response before timeout  
			  --start_addr START_ADDR starting address (default: 0)  
			  --mem_length MEM_LENGTH number of bytes to read (default: 256)  
			  --mem_size MEM_SIZE   numbers of bytes to return per request (default: 16)  
			  --address_byte_size ADDRESS_BYTE_SIZE numbers of bytes of the address (default: 4)  
			  --memory_length_byte_size MEMORY_LENGTH_BYTE_SIZE numbers of bytes of the memory length parameter (default: 2)  
			  --outfile OUTFILE     filename to write output to
			  ```
	</details>
    <details>
	    <summary>auto</summary>
			  ```bash  
			  usage: caringcaribou uds auto [-h] [-min MIN] [-max MAX] [-b B [B ...]] [-ab N] [-sv] [-d D] [-t T] [--min_did MIN_DID] [--max_did MAX_DID]  
  
			 options:  
			  -h, --help            show this help message and exit  
			  -min MIN              min arbitration ID to send request for  
			  -max MAX              max arbitration ID to send request for  
			  -b, --blacklist B [B ...] arbitration IDs to blacklist responses from  
			  -ab, --autoblacklist N listen for false positives for N seconds and blacklist matching arbitration IDs before running discovery  
			  -sv, --skipverify     skip verification step (reduces result accuracy)  
			  -d, --delay D         D seconds delay between messages (default: 0.01)  
			  -t, --timeout T       wait T seconds for response before timeout (default: 0.2)  
			  --min_did MIN_DID     minimum device identifier (DID) to read (default: 0x0000)  
			  --max_did MAX_DID     maximum device identifier (DID) to read (default: 0xFFFF)
			  ```
	</details>
- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/uds_fuzz.md" target="_blank">UDS_Fuzz</a>
    <details>
	    <summary>delay_fuzzer</summary>
			  ```bash  
			  usage: caringcaribou uds_fuzz delay_fuzzer [-h] [-r RTYPE] [-d D] stype target src dst  
  
			 positional arguments:  
			  stype              Describe the session sequence followed by the target ECU.e.g. if the following sequence is needed in order to request a seed: Request 1 - 1003 (Diagnostic Session Control), Request 2 - 1102 (ECUReset), Request 3 - 1005 (Diagnostic Session Control), Request 4 - 2705 (Security Access Seed Request). The option should be: 1003110210052705  
			  target             Seed that is targeted for the delay attack. e.g. 41414141414141  
			  src                arbitration ID to transmit to  
			  dst                arbitration ID to listen to  
  
			 options:  
			  -h, --help         show this help message and exit  
			  -r, --reset RTYPE  Enable reset between security seed requests. Valid RTYPE integers are: 1=hardReset, 2=key off/on, 3=softReset, 4=enable rapid power shutdown, 5=disable rapid power shutdown. This attack is based on hard ECUReset (1) as it targets seed randomness based on the system clock. (default: hardReset)  
			  -d, --delay D      Wait D seconds between the different iterations of security seed request. You'll likely need to increase this when using RTYPE: 1=hardReset. (default: 0.011)
			  ```
	</details>
    <details>
	    <summary>seed_randomness_fuzzer</summary>
			  ```bash  
			  usage: caringcaribou uds_fuzz seed_randomness_fuzzer [-h] [-t ITERATIONS] [-r RTYPE] [-id RTYPE] [-m RMETHOD] [-d D] stype src dst  
  
			 positional arguments:  
			  stype                 Describe the session sequence followed by the target ECU.e.g. if the following sequence is needed in order to request a seed: Request 1 - 1003 (Diagnostic Session Control), Request 2 - 1102 (ECUReset), Request 3 - 1005 (Diagnostic Session Control), Request 4 - 2705 (Security Access Seed Request). The option should be: 1003110210052705  
			  src                   arbitration ID to transmit to  
			  dst                   arbitration ID to listen to  
  
			 options:  
			  -h, --help            show this help message and exit  
			  -t, --iter ITERATIONS Number of iterations of seed requests. It is highly suggested to perform >=1000 for accurate results. (default: 1000)  
			  -r, --reset RTYPE     Enable reset between security seed requests. Valid RTYPE integers are: 1=hardReset, 2=key off/on, 3=softReset, 4=enable rapid power shutdown, 5=disable rapid power shutdown. This attack is based on hard ECUReset (1) as it targets seed randomness based on the system clock. (default: hardReset)  
			  -id, --inter_delay RTYPE Intermediate delay between messages:(default: 0.1)  
			  -m, --reset_method RMETHOD The method that the ECUReset will happen: 1=before each seed request 0=once before the seed requests start (default: 1) *This method works better with option 1.*  
			  -d, --delay D         Wait D seconds between reset and security seed request. You'll likely need to increase this when using RTYPE: 1=hardReset. Does nothing if RTYPE is None. (default: 3.901)
			  ```
	</details>
- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/xcp.md" target="_blank">XCP</a>
    <details>
	    <summary>discovery</summary>
			  ```bash  
			  usage: caringcaribou xcp discovery [-h] [-min MIN] [-max MAX] [-blacklist B [B ...]] [-autoblacklist N]  
  
			 options:  
			  -h, --help            show this help message and exit  
			  -min MIN  
			  -max MAX  
			  -blacklist B [B ...]  arbitration IDs to ignore  
			  -autoblacklist N      scan for interfering signals for N seconds and blacklist matching arbitration IDs
			  ```
	</details>
    <details>
	    <summary>info</summary>
			  ```bash  
			  usage: caringcaribou xcp info [-h] src dst  
  
			 positional arguments:  
			  src         arbitration ID to transmit from  
			  dst         arbitration ID to listen to  
  
			 options:  
			  -h, --help  show this help message and exit
			  ```
	</details>
    <details>
	    <summary>commands</summary>
			  ```bash  
			  usage: caringcaribou xcp commands [-h] src dst  
  
			 positional arguments:  
			  src         arbitration ID to transmit from  
			  dst         arbitration ID to listen to  
  
			 options:  
			  -h, --help  show this help message and exit
			  ```
	</details>
    <details>
	    <summary>dump</summary>
			  ```bash  
			  usage: caringcaribou xcp dump [-h] [-f F] src dst start length  
  
			 positional arguments:  
			  src          arbitration ID to transmit from  
			  dst          arbitration ID to listen to  
			  start        start address  
			  length       dump length  
  
			 options:  
			  -h, --help   show this help message and exit  
			  -f, -file F  output file
			  ```
	</details>			