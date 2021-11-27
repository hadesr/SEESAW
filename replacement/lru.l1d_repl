#include "cache.h"

// initialize replacement state
void CACHE::l1d_initialize_replacement() {
  cout << NAME << " has LRU replacement policy" << endl;
}

// find replacement victim
uint32_t CACHE::l1d_find_victim(uint32_t cpu, uint64_t instr_id, uint32_t set,
                                const BLOCK *current_set, uint64_t ip,
                                uint64_t full_addr, uint32_t type, int partition = -1) {   //partition is default set to -1 for caches other than L1D
  // baseline LRU
  if (partition == -1)
    return lru_victim(cpu, instr_id, set, current_set, ip, full_addr, type);
    else{
        uint32_t way1 = 0, way;
      //in case of l1d, we are finding victim in the particular partition as per partition bit.
     

  // fill invalid line first
  for (way1 = 0; way1 < NUM_WAY/2; way1++) {
    way = way1+ partition*4;
    if (block[set][way].valid == false) {

      DP(if (warmup_complete[cpu]) {
        cout << "[" << NAME << "] " << __func__ << " instr_id: " << instr_id
             << " invalid set: " << set << " way: " << way;
        cout << hex << " address: " << (full_addr >> LOG2_BLOCK_SIZE)
             << " victim address: " << block[set][way].address
             << " data: " << block[set][way].data;
        cout << dec << " lru: " << block[set][way].lru << endl;
      });

      break;
    }
  }

  // LRU victim
  if (way1 == NUM_WAY/2) {
    for (way1 = 0; way1 < NUM_WAY/2; way1++) {
      way = way1 + 4*partition;
      if (block[set][way].lru == NUM_WAY/2 - 1) {

        DP(if (warmup_complete[cpu]) {
          cout << "[" << NAME << "] " << __func__ << " instr_id: " << instr_id
               << " replace set: " << set << " way: " << way;
          cout << hex << " address: " << (full_addr >> LOG2_BLOCK_SIZE)
               << " victim address: " << block[set][way].address
               << " data: " << block[set][way].data;
          cout << dec << " lru: " << block[set][way].lru << endl;
        });

        break;
      }
    }
  }

  if (way1 == NUM_WAY/2) {
    cerr << "[" << NAME << "] " << __func__ << " no victim! set: " << set
         << endl;
    assert(0);
  }

//@Rahul: calculating dead blocks
#ifdef FIND_DEAD_BLOCKS
  if (block[set][way].valid) {
    if (block[set][way].used) {
      used_total++;
    }else{
      unused_total++;
    }

    if (block[set][way].type == LOAD){
      if (block[set][way].used) {
        used_load++;
      }else{
        unused_load++;
      }
    }

    if (block[set][way].type == LOAD_TRANSLATION){
      if (block[set][way].used) {
        used_translations++;
      }else{
        unused_translations++;
      }
    }
  }
#endif

#ifdef FIND_INTERFERENCE
  if (block[set][way].valid) {

    total_eviction++;
    if (block[set][way].type == LOAD_TRANSLATION){
      total_translation_evictions++;
    }

    if (type == LOAD_TRANSLATION) {

      if (block[set][way].type == LOAD_TRANSLATION){
        translation_evicting_translation++;

      }else if (block[set][way].type == LOAD) {
        translation_evicting_load++;
      }

    }else if (type == LOAD){
    
      if (block[set][way].type == LOAD_TRANSLATION){
        load_evicting_translation++;

      }else if (block[set][way].type == LOAD) {
        load_evicting_load++;
      }
    }
  }
#endif

  return way;
}
  
}

// called on every cache hit and cache fill
void CACHE::l1d_update_replacement_state(uint32_t cpu, uint32_t set,
                                         uint32_t way, uint64_t full_addr,
                                         uint64_t ip, uint64_t victim_addr,
                                         uint32_t type, uint8_t hit) {

  if ((type == WRITEBACK) && ip)
    assert(0);

  // uncomment this line to see the LLC accesses
  // cout << "CPU: " << cpu << "  L1D " << setw(9) << TYPE_NAME << " set: " <<
  // setw(5) << set << " way: " << setw(2) << way; cout << hex << " paddr: " <<
  // setw(12) << paddr << " ip: " << setw(8) << ip << " victim_addr: " <<
  // victim_addr << dec << endl;

  // baseline LRU
  if (hit && (type == WRITEBACK)) // writeback hit does not update LRU state
    return;

  //return lru_update(set, way);
  //lru_update is also modified to update lru counter of particular partition
  for (uint32_t j = 0; j < NUM_WAY/2; j++) {  
    uint32_t i = j+ 4*(way/4);
    if (block[set][i].lru < block[set][way].lru) {
      block[set][i].lru++;
    }
  }
  block[set][way].lru = 0;
}

void CACHE::l1d_replacement_final_stats() {}
