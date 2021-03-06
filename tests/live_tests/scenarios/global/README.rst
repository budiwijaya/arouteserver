Global scenario
***************

Built to group as many tests as possible in a single scenario.

- **AS1**:

  AS-SETs:

  - AS-AS1 (1.0.0.0/8, 128.0.0.0/7)
  - AS-AS1_CUSTOMERS (101.0.0.0/16)

  clients:

  - AS1_1 (99.0.2.11)

    - next-hop-self configured in AS1_1.conf
    - next_hop_policy: strict (inherited from general config)

    Announced prefixes:

    ============   ============  ============  ====================================
    Prefix ID      Prefix        AS_PATH       Expected result
    ============   ============  ============  ====================================
    AS1_good1      1.0.1.0/24		       pass
    AS1_good2      1.0.2.0/24                  pass

    bogon1         10.0.0.0/24                 fail prefix_is_bogon
    local1         99.0.2.0/24                 fail prefix_is_in_global_blacklist
    pref_len1      128.0.0.0/7                 fail prefix_len_is_valid
    peer_as1       128.0.0.0/8   [2, 1]        fail bgp_path.first != peer_as
    invalid_asn1   128.0.0.0/9   [1, 65536 1]  fail as_path_contains_invalid_asn
    aspath_len1    128.0.0.0/10  [1, 2x6]      fail bgp_path.len > 6
    ============   ============  ============  ====================================

  - AS1_2 (99.0.2.12)

    - NO next-hop-self in AS1_2.conf (next-hop of AS101 used for AS101_good == 101.0.1.0/24)
    - next_hop_policy: same-as (from clients config)

    Announced prefixes:

    ===========    ===========     ==============  ===========================================
    Prefix ID      Prefix          Feature         Expected result    
    ===========    ===========     ==============  ===========================================
    AS1_good1      1.0.1.0/24
    AS1_good2      1.0.2.0/24
    AS1_good3      1.0.3.0/24      next_hop=AS1_1  win next_hop_is_valid_for_AS1_2 (same-as)
    ===========    ===========     ==============  ===========================================

- **AS2**:

  AS-SETs:

  - AS-AS2 (2.0.0.0/16)
  - AS-AS2_CUSTOMERS (101.0.0.0/16)

  clients:

  - AS2 (99.0.2.21)

    - next-hop-self configured in AS2.conf
    - next_hop_policy: strict (inherited from general config)

    Announced prefixes:

    ==============  ================   =======================================   =================================================
    Prefix ID       Prefix             Feature                                   Expected result
    ==============  ================   =======================================   =================================================
    AS2_good1       2.0.1.0/24
    AS2_good2       2.0.2.0/24

    AS2_blackhole1  2.0.3.1/32         announced with BLACKHOLE 65535:666 comm   propagated with only 65535:666 to AS1_1 and AS3
                                                                                 (AS1_2 has "announce_to_client" = False) and
                                                                                 next-hop 192.0.2.66
    AS2_blackhole2  2.0.3.2/32         announced with local 65534:0 comm         as above
    AS2_blackhole3  2.0.3.3/32         announced with local 65534:0:0 comm       as above
    ==============  ================   =======================================   =================================================

- **AS3**:

  AS-SETs: none

  clients:

  - AS3 (99.0.2.31)

    - no enforcing of origin in AS-SET
    - no enforcing of prefix in AS-SET
    - ADD-PATH enabled
    - passive client-side (no passive on the route server)

    Announced prefixes:

    ================   ============    ==========================================
    Prefix ID          Prefix          Expected result
    ================   ============    ==========================================
    AS3_blacklist1     3.0.1.0/24      fail prefix_is_in_AS3_1_blacklist

    AS3_cc_AS1only     3.0.2.0/24      add 0:999 and 999:1, seen on AS1_1/_2 only
    AS3_cc_not_AS1     3.0.3.0/24      add 0:1, seen on AS2 only
    AS3_cc_none        3.0.4.0/24      add 0:999 , not seen
    AS3_prepend1any    3.0.5.0/24      add 999:65501, AS_PATH 3, 3
    AS3_prepend2any    3.0.6.0/24      add 999:65502, AS_PATH 3, 3, 3
    AS3_prepend3any    3.0.7.0/24      add 999:65503, AS_PATH 3, 3, 3, 3
    ================   ============    ==========================================

- **AS101**:

  clients:

  - Not a route server client, it only peers with AS1_1, AS1_2 and AS2 on 99.0.2.101.

  Annouced prefixes:

  ====================  ============ ========== ==================================================================================
  Prefix ID             Prefix       AS_PATH    Expected result
  ====================  ============ ========== ==================================================================================
  AS101_good1           101.0.1.0/24            fail next_hop_is_valid_for_AS1_2 (for the prefix announce by AS101 to AS1_2)
  AS101_no_rset         101.1.0.0/24            fail prefix_is_in_AS1_1_r_set and prefix_is_in_AS2_1_r_set
  AS102_no_asset        102.0.1.0/24 [101 102]  fail origin_as_in_AS1_1_as_set and origin_as_in_AS2_1_as_set

  AS101_bad_std_comm    101.0.2.0/24            add 65530:0, scrubbed by rs
  AS101_bad_lrg_comm    101.0.3.0/24            add 999:65530:0, scrubbed by rs
  AS101_other_s_comm    101.0.4.0/24            add 888:0, NOT scrubbed by rs
  AS101_other_l_comm    101.0.5.0/24            add 888:0:0, NOT scrubbed by rs
  AS101_bad_good_comms  101.0.6.0/24            add 65530:1,999:65530:1,777:0,777:0:0, 65530 are scrubbed by rs, 777:** are kept
  AS101_transitfree_1   101.0.7.0/24 [101 174]  fail as_path_contains_transit_free_asn
  ====================  ============ ========== ==================================================================================
