# Interface Change

The C/C++ interface changes are described as follows.

## alsa-lib

For details, see [alsa-lib](./LTS_to_SP2_changed_abi_detail/alsa-lib_all_result.md\)

| function | type |
|:----  |:--  |
| snd_tplg_add_object | remove |
| snd_tplg_build | remove |
| snd_tplg_build_file | remove |
| snd_tplg_free | remove |
| snd_tplg_new | remove |
| snd_tplg_set_manifest_data | remove |
| snd_tplg_set_version | remove |
| snd_tplg_verbose | remove |
| snd_config_add_after | add |
| snd_config_add_before | add |
| snd_config_is_array | add |
| snd_dlpath | add |
| snd_mixer_selem_id_parse | add |
| snd_pcm_extplug_set_param_link | add |
| snd_pcm_ioplug_avail | add |
| snd_strlcpy | add |
| snd_use_case_parse_ctl_elem_id | add |
| snd_use_case_parse_selem_id | add |
| __old_snd_pcm_hw_params_set_access_first | change |
| __snd_ctl_elem_info_get_dimension | change |
| _snd_ctl_hw_open | change |
| _snd_rawmidi_hw_open | change |
| snd_midi_event_decode | change |
| snd_pcm_direct_client_chk_xrun | change |
| snd_pcm_dmix_open | change |
| snd_use_case_get | change |

## atk

For details, see [atk](./LTS_to_SP2_changed_abi_detail/atk_all_result.md\)

| function | type |
|:----  |:--  |
| atk_object_get_accessible_id | add |
| atk_object_set_accessible_id | add |
| atk_plug_set_child | add |
| atk_text_scroll_substring_to | add |
| atk_text_scroll_substring_to_point | add |
| atk_add_focus_tracker | change |
| atk_text_attribute_for_name | change |

## brltty

For details, see [brltty](./LTS_to_SP2_changed_abi_detail/brltty_all_result.md\)

| function | type |
|:----  |:--  |
| lxBrailleOfflineListener | remove |
| lxBrailleDeviceOfflineListener | add |

## c-ares

For details, see [c-ares](./LTS_to_SP2_changed_abi_detail/c-ares_all_result.md\)

| function | type |
|:----  |:--  |
| ares_freeaddrinfo | add |
| ares_getaddrinfo | add |

## e2fsprogs

For details, see [e2fsprogs](./LTS_to_SP2_changed_abi_detail/e2fsprogs_all_result.md\)

| function | type |
|:----  |:--  |
| e2p_feature_to_string | add |
| list_super | change |
| ext2fs_dirent_swab_in | add |
| ext2fs_dirent_swab_in2 | add |
| ext2fs_dirent_swab_out | add |
| ext2fs_dirent_swab_out2 | add |
| ext2fs_get_stat_i_blocks | add |
| ext2fs_resize_array | add |
| ext2fs_swap_ext_attr | add |
| ext2fs_swap_ext_attr_entry | add |
| ext2fs_swap_ext_attr_header | add |
| ext2fs_swap_group_desc | add |
| ext2fs_swap_group_desc2 | add |
| ext2fs_swap_inode | add |
| ext2fs_swap_inode_full | add |
| ext2fs_swap_mmp | add |
| ext2fs_swap_super | add |
| ext2fs_add_journal_device | change |
| ext2fs_file_get_size | change |
| ext2fs_file_lseek | change |
| ext2fs_file_set_size | change |
| ext2fs_file_set_size2 | change |
| ext2fs_inode_size_set | change |
| ext2fs_mmp_csum_set | change |

## exiv2

For details, see [exiv2](./LTS_to_SP2_changed_abi_detail/exiv2_all_result.md\)

| function | type |
|:----  |:--  |
| None | None |

## fontconfig

For details, see [fontconfig](./LTS_to_SP2_changed_abi_detail/fontconfig_all_result.md\)

| function | type |
|:----  |:--  |
| IA__FcConfigGetFilename | add |
| IA__FcStrBuildFilename | add |
| FcConfigAddRule | change |
| IA__FcCacheCreateTagFile | change |
| IA__FcConfigGetCacheDirs | change |

## glib2

For details, see [glib2](./LTS_to_SP2_changed_abi_detail/glib2_all_result.md\)

| function | type |
|:----  |:--  |
| g_file_monitor_source_handle_event | change |

### gnutls

For details, see [gnutls](./LTS_to_SP2_changed_abi_detail/gnutls_all_result.md\)

| function | type |
|:----  |:--  |
| gnutls::certificate_credentials::set_retrieve_function | change |
| _gnutls_buffer_clear | add |
| _gnutls_buffer_pop_datum | add |
| _gnutls_buffer_unescape | add |
| _gnutls_iov_iter_init | add |
| _gnutls_iov_iter_next | add |
| _gnutls_iov_iter_sync | add |
| gnutls_aead_cipher_decryptv2 | add |
| gnutls_aead_cipher_encryptv2 | add |
| gnutls_certificate_verification_profile_get_id | add |
| gnutls_certificate_verification_profile_get_name | add |
| gnutls_ext_get_name2 | add |
| gnutls_hkdf_expand | add |
| gnutls_hkdf_extract | add |
| gnutls_hmac_get_key_size | add |
| gnutls_pbkdf2 | add |
| gnutls_pkcs7_print_signature_info | add |
| gnutls_prf_hash_get | add |
| gnutls_psk_server_get_username2 | add |
| gnutls_psk_set_client_credentials2 | add |
| gnutls_psk_set_client_credentials_function2 | add |
| gnutls_psk_set_server_credentials_function2 | add |
| gnutls_session_get_keylog_function | add |
| gnutls_session_set_keylog_function | add |
| _gnutls13_psk_ext_iter_next_binder | change |
| _gnutls_cipher_get_iv | change |
| _gnutls_ecc_curve_is_supported | change |
| _gnutls_hello_set_default_version | change |
| gnutls_ocsp_req_export | change |
| gnutls_ocsp_req_get_extension | change |
| gnutls_ocsp_req_get_nonce | change |
| gnutls_ocsp_req_get_version | change |
| gnutls_ocsp_req_print | change |
| gnutls_ocsp_resp_check_crt | change |
| gnutls_ocsp_resp_export | change |
| gnutls_ocsp_resp_export2 | change |
| gnutls_ocsp_resp_get_certs | change |
| gnutls_ocsp_resp_get_extension | change |
| gnutls_ocsp_resp_get_nonce | change |
| gnutls_ocsp_resp_get_produced | change |
| gnutls_ocsp_resp_get_responder2 | change |
| gnutls_ocsp_resp_get_responder_raw_id | change |
| gnutls_ocsp_resp_get_response | change |
| gnutls_ocsp_resp_get_signature_algorithm | change |
| gnutls_ocsp_resp_print | change |
| gnutls_ocsp_resp_verify | change |
| gnutls_ocsp_resp_verify_direct | change |
| gnutls_ocsp_status_request_is_checked | change |
| gnutls_privkey_set_spki | change |
| gnutls_psk_allocate_client_credentials | change |
| gnutls_psk_allocate_server_credentials | change |
| gnutls_pubkey_get_spki | change |
| gnutls_x509_crq_get_spki | change |
| gnutls_x509_crq_get_tlsfeatures | change |
| gnutls_x509_crt_get_spki | change |
| gnutls_x509_privkey_get_spki | change |
| gnutls_x509_spki_deinit | change |
| gnutls_x509_spki_init | change |
| dane_query_data | change |
| dane_verify_session_crt | change |

## grpc 

For details, see [grpc](./LTS_to_SP2_changed_abi_detail/grpc_all_result.md\)

| function | type |
|:----  |:--  |
| None | None |

## haveged 

For details, see [haveged](./LTS_to_SP2_changed_abi_detail/haveged_all_result.md\)

| function | type |
|:----  |:--  |
| havege_reparent | add |

## libcap-ng 

For details, see [libcap-ng](./LTS_to_SP2_changed_abi_detail/libcap-ng_all_result.md\)

| function | type |
|:----  |:--  |
| capng_have_permitted_capabilities | add |

## libcomps 

For details, see [libcomps](./LTS_to_SP2_changed_abi_detail/libcomps_all_result.md\)

| function | type |
|:----  |:--  |
| None | None |

## libdnf 

For details, see [libdnf](./LTS_to_SP2_changed_abi_detail/libdnf_all_result.md\)

| function | type |
|:----  |:--  |
| libdnf::DependencyContainer::DependencyContainer | remove |
| libdnf::File::OpenException::OpenException | remove |
| libdnf::ModuleDefaultsContainer::ModuleDefaultsContainer | remove |
| libdnf::ModuleDefaultsContainer::fromString | remove |
| libdnf::ModuleDefaultsContainer::getDefaultProfiles | remove |
| libdnf::ModuleDefaultsContainer::getDefaultStreamFor | remove |
| libdnf::ModuleDefaultsContainer::getDefaultStreams | remove |
| libdnf::ModuleDefaultsContainer::reportFailures | remove |
| libdnf::ModuleDefaultsContainer::resolve | remove |
| libdnf::ModuleDefaultsContainer::saveDefaults | remove |
| libdnf::ModuleDefaultsContainer::~ModuleDefaultsContainer | remove |
| libdnf::ModuleDependencies::getRequirements | remove |
| libdnf::ModuleDependencies::wrapModuleDependencies | remove |
| libdnf::ModuleMetadata::ModuleMetadata | remove |
| libdnf::ModuleMetadata::getArchitecture | remove |
| libdnf::ModuleMetadata::getArtifacts | remove |
| libdnf::ModuleMetadata::getContext | remove |
| libdnf::ModuleMetadata::getDependencies | remove |
| libdnf::ModuleMetadata::getDescription | remove |
| libdnf::ModuleMetadata::getName | remove |
| libdnf::ModuleMetadata::getProfiles | remove |
| libdnf::ModuleMetadata::getStream | remove |
| libdnf::ModuleMetadata::getSummary | remove |
| libdnf::ModuleMetadata::getVersion | remove |
| libdnf::ModuleMetadata::getYaml | remove |
| libdnf::ModuleMetadata::metadataFromString | remove |
| libdnf::ModuleMetadata::wrapModulemdModule | remove |
| libdnf::ModuleMetadata::~ModuleMetadata | remove |
| libdnf::ModulePackage::ModulePackage | remove |
| libdnf::Repo::Impl::lrHandleInitRemote | remove |
| libdnf::Swdb::beginTransaction | remove |

## libdrm

For details, see [libdrm](./LTS_to_SP2_changed_abi_detail/libdrm_all_result.md\)

| function | type |
|:----  |:--  |
| drmIsMaster | add |
| drmModeFreeFB2 | add |
| drmModeGetFB2 | add |
| drmSyncobjQuery | add |
| drmSyncobjQuery2 | add |
| drmSyncobjTimelineSignal | add |
| drmSyncobjTimelineWait | add |
| drmSyncobjTransfer | add |
| amdgpu_bo_list_create_raw | add |
| amdgpu_bo_list_destroy_raw | add |
| amdgpu_cs_ctx_override_priority | add |
| amdgpu_cs_query_reset_state2 | add |
| amdgpu_cs_submit_raw2 | add |
| amdgpu_cs_syncobj_export_sync_file2 | add |
| amdgpu_cs_syncobj_import_sync_file2 | add |
| amdgpu_cs_syncobj_query | add |
| amdgpu_cs_syncobj_query2 | add |
| amdgpu_cs_syncobj_timeline_signal | add |
| amdgpu_cs_syncobj_timeline_wait | add |
| amdgpu_cs_syncobj_transfer | add |
| amdgpu_bo_alloc | change |
| amdgpu_bo_import | change |
| amdgpu_bo_list_create | change |
| amdgpu_bo_va_op_raw | change |
| amdgpu_create_bo_from_user_mem | change |
| amdgpu_cs_chunk_fence_to_dep | change |
| amdgpu_cs_create_syncobj | change |
| amdgpu_cs_create_syncobj2 | change |
| amdgpu_cs_ctx_create | change |
| amdgpu_cs_ctx_create2 | change |
| amdgpu_cs_destroy_syncobj | change |
| amdgpu_cs_export_syncobj | change |
| amdgpu_cs_fence_to_handle | change |
| amdgpu_cs_import_syncobj | change |
| amdgpu_cs_submit_raw | change |
| amdgpu_cs_syncobj_import_sync_file | change |
| amdgpu_cs_syncobj_reset | change |
| amdgpu_cs_syncobj_wait | change |
| amdgpu_device_deinitialize | change |
| amdgpu_device_initialize | change |
| amdgpu_find_bo_by_cpu_mapping | change |
| amdgpu_get_marketing_name | change |
| amdgpu_query_buffer_size_alignment | change |
| amdgpu_query_crtc_from_id | change |
| amdgpu_query_firmware_version | change |
| amdgpu_query_gds_info | change |
| amdgpu_query_gpu_info | change |
| amdgpu_query_heap_info | change |
| amdgpu_query_hw_ip_count | change |
| amdgpu_query_hw_ip_info | change |
| amdgpu_query_info | change |
| amdgpu_query_sw_info | change |
| amdgpu_read_mm_registers | change |
| amdgpu_va_range_alloc | change |
| amdgpu_va_range_query | change |
| fd_ringbuffer_emit_reloc_ring | remove |
| fd_ringmarker_del | remove |
| fd_ringmarker_dwords | remove |
| fd_ringmarker_flush | remove |
| fd_ringmarker_mark | remove |
| fd_ringmarker_new | remove |
| fd_ringbuffer_new_flags | add |
| fd_ringbuffer_ref | add |
| fd_bo_cpu_fini | change |

## libell 

For details, see [libell](./LTS_to_SP2_changed_abi_detail/libell_all_result.md\)

| function | type |
|:----  |:--  |
| l_fswatch_destroy | remove |
| l_fswatch_new | remove |
| l_genl_family_can_dump | remove |
| l_genl_family_can_send | remove |
| l_genl_family_get_version | remove |
| l_genl_family_has_group | remove |
| l_genl_family_ref | remove |
| l_genl_family_set_watches | remove |
| l_genl_family_unref | remove |
| l_genl_new_default | remove |
| l_genl_set_close_on_unref | remove |
| l_genl_set_unicast_handler | remove |
| l_pem_load_certificate | remove |
| l_aead_cipher_is_supported | add |
| l_cert_free | add |
| l_cert_get_der_data | add |
| l_cert_get_dn | add |
| l_cert_get_pubkey | add |
| l_cert_get_pubkey_type | add |
| l_cert_new_from_der | add |
| l_certchain_free | add |
| l_certchain_get_leaf | add |
| l_certchain_verify | add |
| l_certchain_walk_from_ca | add |
| l_certchain_walk_from_leaf | add |
| l_checksum_digest_length | add |
| l_cipher_is_supported | add |
| l_dbus_object_get_data | add |
| l_dhcp_client_set_debug | add |
| l_dhcp_lease_get_dns | add |
| l_dhcp_lease_get_domain_name | add |
| l_dir_watch_destroy | add |
| l_dir_watch_new | add |
| l_ecc_curve_get | add |
| l_ecc_curve_get_ike_group | add |
| l_ecc_curve_get_name | add |
| l_ecc_curve_get_order | add |
| l_ecc_curve_get_prime | add |
| l_ecc_curve_get_scalar_bytes | add |
| l_ecc_curve_get_supported_ike_groups | add |
| l_ecc_curve_get_supported_tls_groups | add |
| l_ecc_curve_get_tls_group | add |
| l_ecc_point_add | add |
| l_ecc_point_free | add |
| l_ecc_point_from_data | add |
| l_ecc_point_get_data | add |
| l_ecc_point_get_x | add |
| l_ecc_point_inverse | add |
| l_ecc_point_multiply | add |
| l_ecc_point_new | add |
| l_ecc_points_are_equal | add |
| l_ecc_scalar_add | add |
| l_ecc_scalar_free | add |
| l_ecc_scalar_get_data | add |
| l_ecc_scalar_legendre | add |
| l_ecc_scalar_multiply | add |
| l_ecc_scalar_new | add |
| l_ecc_scalar_new_random | add |
| l_ecc_scalar_sum_x | add |
| l_ecc_scalars_are_equal | add |
| l_ecdh_generate_key_pair | add |
| l_ecdh_generate_shared_secret | add |
| l_genl_add_family_watch | add |
| l_genl_add_unicast_watch | add |
| l_genl_discover_families | add |
| l_genl_family_free | add |
| l_genl_family_get_info | add |
| l_genl_family_info_can_dump | add |
| l_genl_family_info_can_send | add |
| l_genl_family_info_get_groups | add |
| l_genl_family_info_get_id | add |
| l_genl_family_info_get_name | add |
| l_genl_family_info_get_version | add |
| l_genl_family_info_has_group | add |
| l_genl_msg_get_extended_error | add |
| l_genl_msg_new_from_data | add |
| l_genl_msg_to_data | add |
| l_genl_remove_family_watch | add |
| l_genl_remove_unicast_watch | add |
| l_genl_request_family | add |
| l_getrandom_uint32 | add |
| l_gpio_chip_find_line_offset | add |
| l_gpio_chip_free | add |
| l_gpio_chip_get_label | add |
| l_gpio_chip_get_line_consumer | add |
| l_gpio_chip_get_line_label | add |
| l_gpio_chip_get_name | add |
| l_gpio_chip_get_num_lines | add |
| l_gpio_chip_new | add |
| l_gpio_chips_with_line_label | add |
| l_gpio_reader_free | add |
| l_gpio_reader_get | add |
| l_gpio_reader_new | add |
| l_gpio_writer_free | add |
| l_gpio_writer_new | add |
| l_gpio_writer_set | add |
| l_key_generate_dh_private | add |
| l_key_get_info | add |
| l_key_is_supported | add |
| l_key_validate_dh_payload | add |
| l_keyring_link | add |
| l_keyring_link_nested | add |
| l_keyring_unlink | add |
| l_keyring_unlink_nested | add |
| l_net_hostname_is_localhost | add |
| l_net_hostname_is_root | add |
| l_path_find | add |
| l_path_get_mtime | add |
| l_path_next | add |
| l_path_touch | add |
| l_pem_load_certificate_chain | add |
| l_pem_load_certificate_chain_from_data | add |
| l_pem_load_certificate_list | add |
| l_pem_load_certificate_list_from_data | add |
| l_pem_load_private_key_from_data | add |
| l_ringbuf_append | add |
| l_rtnl_ifaddr4_add | add |
| l_rtnl_ifaddr4_delete | add |
| l_rtnl_ifaddr4_dump | add |
| l_rtnl_ifaddr4_extract | add |
| l_rtnl_ifaddr6_add | add |
| l_rtnl_ifaddr6_delete | add |
| l_rtnl_ifaddr6_dump | add |
| l_rtnl_ifaddr6_extract | add |
| l_rtnl_route4_add_connected | add |
| l_rtnl_route4_add_gateway | add |
| l_rtnl_route4_dump | add |
| l_rtnl_route4_extract | add |
| l_rtnl_route6_add_gateway | add |
| l_rtnl_route6_delete_gateway | add |
| l_rtnl_route6_dump | add |
| l_rtnl_route6_extract | add |
| l_rtnl_set_linkmode_and_operstate | add |
| l_rtnl_set_mac | add |
| l_rtnl_set_powered | add |
| l_settings_get_embedded_groups | add |
| l_settings_get_embedded_value | add |
| l_settings_has_embedded_group | add |
| l_strv_append | add |
| l_strv_append_printf | add |
| l_strv_append_vprintf | add |
| l_strv_copy | add |
| l_strv_free | add |
| l_strv_new | add |
| l_time_now | add |
| l_timeout_set_callback | add |
| l_tls_prf_get_bytes | add |
| l_tls_set_debug | add |
| l_tls_set_domain_mask | add |
| l_tls_set_version_range | add |
| l_tls_start | add |
| l_uintset_find_unused | add |
| l_uintset_foreach | add |
| l_uintset_intersect | add |
| l_uintset_isempty | add |
| l_utf8_from_ucs2be | add |
| l_utf8_from_wchar | add |
| l_utf8_to_ucs2be | add |
| l_util_hexstring_upper | add |
| l_uuid_from_string | add |
| l_uuid_v4 | add |

## libgusb

For details, see [libgusb](./LTS_to_SP2_changed_abi_detail/libgusb_all_result.md\)

| function | type |
|:----  |:--  |
| g_usb_interface_get_type | remove |
| g_usb_source_set_callback | remove |
| g_usb_device_get_spec | add |
| g_usb_endpoint_get_address | add |
| g_usb_endpoint_get_direction | add |
| g_usb_endpoint_get_extra | add |
| g_usb_endpoint_get_kind | add |
| g_usb_endpoint_get_maximum_packet_size | add |
| g_usb_endpoint_get_number | add |
| g_usb_endpoint_get_polling_interval | add |
| g_usb_endpoint_get_refresh | add |
| g_usb_endpoint_get_synch_address | add |
| g_usb_endpoint_get_type | add |
| g_usb_interface_get_endpoints | add |
| g_usb_version_string | add |
| g_usb_device_get_interface | change |

## libical

For details, see [libical](./LTS_to_SP2_changed_abi_detail/libical_all_result.md\)

| function | type |
|:----  |:--  |
| icalproperty_get_datetime_with_component | add |
| icaltimezone_truncate_vtimezone | add |
| icalattach_new_from_data | change |
| i_cal_array_append | remove |
| i_cal_array_element_at | remove |
| i_cal_array_new | remove |
| i_cal_component_as_ical_string_r | remove |
| i_cal_component_new_clone | remove |
| i_cal_component_string_to_kind | remove |
| i_cal_datetimeperiod_type_get_period | remove |
| i_cal_datetimeperiod_type_get_time | remove |
| i_cal_datetimeperiod_type_get_type | remove |
| i_cal_datetimeperiod_type_set_period | remove |
| i_cal_datetimeperiod_type_set_time | remove |
| i_cal_duration_type_as_ical_string_r | remove |
| i_cal_duration_type_as_int | remove |
| i_cal_duration_type_bad_duration | remove |
| i_cal_duration_type_from_int | remove |
| i_cal_duration_type_from_string | remove |
| i_cal_duration_type_get_days | remove |
| i_cal_duration_type_get_hours | remove |
| i_cal_duration_type_get_minutes | remove |
| i_cal_duration_type_get_seconds | remove |
| i_cal_duration_type_get_type | remove |
| i_cal_duration_type_get_weeks | remove |
| i_cal_duration_type_is_bad_duration | remove |
| i_cal_duration_type_is_neg | remove |
| i_cal_duration_type_is_null_duration | remove |
| i_cal_duration_type_null_duration | remove |
| i_cal_duration_type_set_days | remove |
| i_cal_duration_type_set_hours | remove |
| i_cal_duration_type_set_is_neg | remove |
| i_cal_duration_type_set_minutes | remove |
| i_cal_duration_type_set_seconds | remove |
| i_cal_duration_type_set_weeks | remove |
| i_cal_enum_num_to_reqstat | remove |
| i_cal_enum_reqstat_code_r | remove |
| i_cal_enum_reqstat_desc | remove |
| i_cal_enum_reqstat_major | remove |
| i_cal_enum_reqstat_minor | remove |
| i_cal_geo_type_get_lat | remove |
| i_cal_geo_type_get_lon | remove |
| i_cal_geo_type_get_type | remove |
| i_cal_geo_type_new_default | remove |
| i_cal_geo_type_set_lat | remove |
| i_cal_geo_type_set_lon | remove |
| i_cal_langbind_access_array | remove |
| i_cal_langbind_free_array | remove |
| i_cal_langbind_get_first_component | remove |
| i_cal_langbind_get_first_parameter | remove |
| i_cal_langbind_get_first_property | remove |
| i_cal_langbind_get_next_component | remove |
| i_cal_langbind_get_next_parameter | remove |
| i_cal_langbind_get_next_property | remove |
| i_cal_langbind_new_array | remove |
| i_cal_langbind_property_eval_string_r | remove |
| i_cal_langbind_quote_as_ical_r | remove |
| i_cal_langbind_string_to_open_flag | remove |
| i_cal_parameter_as_ical_string_r | remove |
| i_cal_parameter_new_clone | remove |
| i_cal_parameter_string_to_kind | remove |
| i_cal_parser_set_gen_data | remove |
| i_cal_parser_string_line_generator | remove |
| i_cal_period_type_as_ical_string_r | remove |
| i_cal_period_type_from_string | remove |
| i_cal_period_type_get_duration | remove |
| i_cal_period_type_get_end | remove |
| i_cal_period_type_get_start | remove |
| i_cal_period_type_get_type | remove |
| i_cal_period_type_is_null_period | remove |
| i_cal_period_type_is_valid_period | remove |
| i_cal_period_type_null_period | remove |
| i_cal_period_type_set_duration | remove |
| i_cal_period_type_set_end | remove |
| i_cal_period_type_set_start | remove |
| i_cal_property_as_ical_string_r | remove |
| i_cal_property_enum_belongs_to_property | remove |
| i_cal_property_enum_to_string_r | remove |
| i_cal_property_get_parameter_as_string_r | remove |
| i_cal_property_get_property_name_r | remove |
| i_cal_property_get_value_as_string_r | remove |
| i_cal_property_new_clone | remove |
| i_cal_property_string_to_kind | remove |
| i_cal_property_string_to_method | remove |
| i_cal_property_string_to_status | remove |
| i_cal_property_value_kind_to_kind | remove |
| i_cal_recur_freq_to_string | remove |
| i_cal_recur_skip_to_string | remove |
| i_cal_recur_string_to_freq | remove |
| i_cal_recur_string_to_skip | remove |
| i_cal_recur_string_to_weekday | remove |
| i_cal_recur_weekday_to_string | remove |
| i_cal_recurrence_type_as_string_r | remove |
| i_cal_recurrence_type_clear | remove |
| i_cal_recurrence_type_day_day_of_week | remove |
| i_cal_recurrence_type_day_position | remove |
| i_cal_recurrence_type_from_string | remove |
| i_cal_recurrence_type_get_by_day | remove |
| i_cal_recurrence_type_get_by_hour | remove |
| i_cal_recurrence_type_get_by_minute | remove |
| i_cal_recurrence_type_get_by_month | remove |
| i_cal_recurrence_type_get_by_month_day | remove |
| i_cal_recurrence_type_get_by_second | remove |
| i_cal_recurrence_type_get_by_set_pos | remove |
| i_cal_recurrence_type_get_by_week_no | remove |
| i_cal_recurrence_type_get_by_year_day | remove |
| i_cal_recurrence_type_get_count | remove |
| i_cal_recurrence_type_get_freq | remove |
| i_cal_recurrence_type_get_interval | remove |
| i_cal_recurrence_type_get_type | remove |
| i_cal_recurrence_type_get_until | remove |
| i_cal_recurrence_type_get_week_start | remove |
| i_cal_recurrence_type_month_is_leap | remove |
| i_cal_recurrence_type_month_month | remove |
| i_cal_recurrence_type_rscale_is_supported | remove |
| i_cal_recurrence_type_rscale_supported_calendars | remove |
| i_cal_recurrence_type_set_by_day | remove |
| i_cal_recurrence_type_set_by_hour | remove |
| i_cal_recurrence_type_set_by_minute | remove |
| i_cal_recurrence_type_set_by_month | remove |
| i_cal_recurrence_type_set_by_month_day | remove |
| i_cal_recurrence_type_set_by_second | remove |
| i_cal_recurrence_type_set_by_set_pos | remove |
| i_cal_recurrence_type_set_by_week_no | remove |
| i_cal_recurrence_type_set_by_year_day | remove |
| i_cal_recurrence_type_set_count | remove |
| i_cal_recurrence_type_set_freq | remove |
| i_cal_recurrence_type_set_interval | remove |
| i_cal_recurrence_type_set_until | remove |
| i_cal_recurrence_type_set_week_start | remove |
| i_cal_reqstat_type_as_string_r | remove |
| i_cal_reqstat_type_from_string | remove |
| i_cal_reqstat_type_get_code | remove |
| i_cal_reqstat_type_get_debug | remove |
| i_cal_reqstat_type_get_desc | remove |
| i_cal_reqstat_type_get_type | remove |
| i_cal_reqstat_type_set_code | remove |
| i_cal_time_as_ical_string_r | remove |
| i_cal_time_current_time_with_zone | remove |
| i_cal_time_from_day_of_year | remove |
| i_cal_time_from_string | remove |
| i_cal_time_from_timet_with_zone | remove |
| i_cal_time_null_date | remove |
| i_cal_time_null_time | remove |
| i_cal_time_span_is_busy | remove |
| i_cal_time_tiemzone_expand_vtimezone | remove |
| i_cal_time_today | remove |
| i_cal_timetype_get_day | remove |
| i_cal_timetype_get_hour | remove |
| i_cal_timetype_get_minute | remove |
| i_cal_timetype_get_month | remove |
| i_cal_timetype_get_second | remove |
| i_cal_timetype_get_type | remove |
| i_cal_timetype_get_year | remove |
| i_cal_timetype_get_zone | remove |
| i_cal_timetype_is_date | remove |
| i_cal_timetype_is_daylight | remove |
| i_cal_timetype_is_utc | remove |
| i_cal_timetype_new | remove |
| i_cal_timetype_set_day | remove |
| i_cal_timetype_set_hour | remove |
| i_cal_timetype_set_is_date | remove |
| i_cal_timetype_set_is_daylight | remove |
| i_cal_timetype_set_minute | remove |
| i_cal_timetype_set_month | remove |
| i_cal_timetype_set_second | remove |
| i_cal_timetype_set_year | remove |
| i_cal_timezone_convert_time | remove |
| i_cal_timezone_phase_get_comment | remove |
| i_cal_timezone_phase_get_dtstart | remove |
| i_cal_timezone_phase_get_offsetto | remove |
| i_cal_timezone_phase_get_rdate | remove |
| i_cal_timezone_phase_get_rrule | remove |
| i_cal_timezone_phase_get_type | remove |
| i_cal_timezone_phase_get_tzname | remove |
| i_cal_timezone_phase_get_tzoffsetfrom | remove |
| i_cal_timezone_phase_is_stdandard | remove |
| i_cal_timezone_phase_set_dtstart | remove |
| i_cal_timezone_phase_set_is_stdandard | remove |
| i_cal_timezone_phase_set_offsetto | remove |
| i_cal_timezone_phase_set_rdate | remove |
| i_cal_timezone_phase_set_tzoffsetfrom | remove |
| i_cal_timezonetype_get_last_mod | remove |
| i_cal_timezonetype_get_type | remove |
| i_cal_timezonetype_get_tzid | remove |
| i_cal_timezonetype_get_tzurl | remove |
| i_cal_timezonetype_set_last_mod | remove |
| i_cal_trigger_type_from_int | remove |
| i_cal_trigger_type_from_string | remove |
| i_cal_trigger_type_get_duration | remove |
| i_cal_trigger_type_get_time | remove |
| i_cal_trigger_type_get_type | remove |
| i_cal_trigger_type_is_bad_trigger | remove |
| i_cal_trigger_type_is_null_trigger | remove |
| i_cal_trigger_type_set_duration | remove |
| i_cal_trigger_type_set_time | remove |
| i_cal_value_as_ical_string_r | remove |
| i_cal_value_new_clone | remove |
| i_cal_value_string_to_kind | remove |
| i_cal_attach_new_from_bytes | add |
| i_cal_component_as_ical_string | add |
| i_cal_component_clone | add |
| i_cal_component_foreach_recurrence | add |
| i_cal_component_get_parent | add |
| i_cal_component_kind_from_string | add |
| i_cal_component_set_parent | add |
| i_cal_component_take_component | add |
| i_cal_component_take_property | add |
| i_cal_datetimeperiod_get_period | add |
| i_cal_datetimeperiod_get_time | add |
| i_cal_datetimeperiod_get_type | add |
| i_cal_datetimeperiod_new | add |
| i_cal_datetimeperiod_set_period | add |
| i_cal_datetimeperiod_set_time | add |
| i_cal_duration_as_ical_string | add |
| i_cal_duration_as_int | add |
| i_cal_duration_get_days | add |
| i_cal_duration_get_hours | add |
| i_cal_duration_get_minutes | add |
| i_cal_duration_get_seconds | add |
| i_cal_duration_get_type | add |
| i_cal_duration_get_weeks | add |
| i_cal_duration_is_bad_duration | add |
| i_cal_duration_is_neg | add |
| i_cal_duration_is_null_duration | add |
| i_cal_duration_new_bad_duration | add |
| i_cal_duration_new_from_int | add |
| i_cal_duration_new_from_string | add |
| i_cal_duration_new_null_duration | add |
| i_cal_duration_set_days | add |
| i_cal_duration_set_hours | add |
| i_cal_duration_set_is_neg | add |
| i_cal_duration_set_minutes | add |
| i_cal_duration_set_seconds | add |
| i_cal_duration_set_weeks | add |
| i_cal_geo_clone | add |
| i_cal_geo_get_lat | add |
| i_cal_geo_get_lon | add |
| i_cal_geo_get_type | add |
| i_cal_geo_new | add |
| i_cal_geo_set_lat | add |
| i_cal_geo_set_lon | add |
| i_cal_object_free_global_objects | add |
| i_cal_parameter_as_ical_string | add |
| i_cal_parameter_clone | add |
| i_cal_parameter_kind_from_string | add |
| i_cal_period_as_ical_string | add |
| i_cal_period_get_duration | add |
| i_cal_period_get_end | add |
| i_cal_period_get_start | add |
| i_cal_period_get_type | add |
| i_cal_period_is_null_period | add |
| i_cal_period_is_valid_period | add |
| i_cal_period_new_from_string | add |
| i_cal_period_new_null_period | add |
| i_cal_period_set_duration | add |
| i_cal_period_set_end | add |
| i_cal_period_set_start | add |
| i_cal_property_as_ical_string | add |
| i_cal_property_clone | add |
| i_cal_property_enum_to_string | add |
| i_cal_property_get_color | add |
| i_cal_property_get_datetime_with_component | add |
| i_cal_property_get_parameter_as_string | add |
| i_cal_property_get_property_name | add |
| i_cal_property_get_value_as_string | add |
| i_cal_property_kind_from_string | add |
| i_cal_property_kind_has_property | add |
| i_cal_property_method_from_string | add |
| i_cal_property_new_color | add |
| i_cal_property_set_color | add |
| i_cal_property_status_from_string | add |
| i_cal_property_take_parameter | add |
| i_cal_property_take_value | add |
| i_cal_recurrence_clear | add |
| i_cal_recurrence_day_day_of_week | add |
| i_cal_recurrence_day_position | add |
| i_cal_recurrence_frequency_from_string | add |
| i_cal_recurrence_frequency_to_string | add |
| i_cal_recurrence_get_by_day | add |
| i_cal_recurrence_get_by_day_array | add |
| i_cal_recurrence_get_by_hour | add |
| i_cal_recurrence_get_by_hour_array | add |
| i_cal_recurrence_get_by_minute | add |
| i_cal_recurrence_get_by_minute_array | add |
| i_cal_recurrence_get_by_month | add |
| i_cal_recurrence_get_by_month_array | add |
| i_cal_recurrence_get_by_month_day | add |
| i_cal_recurrence_get_by_month_day_array | add |
| i_cal_recurrence_get_by_second | add |
| i_cal_recurrence_get_by_second_array | add |
| i_cal_recurrence_get_by_set_pos | add |
| i_cal_recurrence_get_by_set_pos_array | add |
| i_cal_recurrence_get_by_week_no | add |
| i_cal_recurrence_get_by_week_no_array | add |
| i_cal_recurrence_get_by_year_day | add |
| i_cal_recurrence_get_by_year_day_array | add |
| i_cal_recurrence_get_count | add |
| i_cal_recurrence_get_freq | add |
| i_cal_recurrence_get_interval | add |
| i_cal_recurrence_get_type | add |
| i_cal_recurrence_get_until | add |
| i_cal_recurrence_get_week_start | add |
| i_cal_recurrence_month_is_leap | add |
| i_cal_recurrence_month_month | add |
| i_cal_recurrence_new | add |
| i_cal_recurrence_new_from_string | add |
| i_cal_recurrence_rscale_is_supported | add |
| i_cal_recurrence_rscale_supported_calendars | add |
| i_cal_recurrence_set_by_day | add |
| i_cal_recurrence_set_by_day_array | add |
| i_cal_recurrence_set_by_hour | add |
| i_cal_recurrence_set_by_hour_array | add |
| i_cal_recurrence_set_by_minute | add |
| i_cal_recurrence_set_by_minute_array | add |
| i_cal_recurrence_set_by_month | add |
| i_cal_recurrence_set_by_month_array | add |
| i_cal_recurrence_set_by_month_day | add |
| i_cal_recurrence_set_by_month_day_array | add |
| i_cal_recurrence_set_by_second | add |
| i_cal_recurrence_set_by_second_array | add |
| i_cal_recurrence_set_by_set_pos | add |
| i_cal_recurrence_set_by_set_pos_array | add |
| i_cal_recurrence_set_by_week_no | add |
| i_cal_recurrence_set_by_week_no_array | add |
| i_cal_recurrence_set_by_year_day | add |
| i_cal_recurrence_set_by_year_day_array | add |
| i_cal_recurrence_set_count | add |
| i_cal_recurrence_set_freq | add |
| i_cal_recurrence_set_interval | add |
| i_cal_recurrence_set_until | add |
| i_cal_recurrence_set_week_start | add |
| i_cal_recurrence_skip_from_string | add |
| i_cal_recurrence_skip_to_string | add |
| i_cal_recurrence_to_string | add |
| i_cal_recurrence_weekday_from_string | add |
| i_cal_recurrence_weekday_to_string | add |
| i_cal_reqstat_get_code | add |
| i_cal_reqstat_get_debug | add |
| i_cal_reqstat_get_desc | add |
| i_cal_reqstat_get_type | add |
| i_cal_reqstat_new_from_string | add |
| i_cal_reqstat_set_code | add |
| i_cal_reqstat_to_string | add |
| i_cal_request_status_code | add |
| i_cal_request_status_desc | add |
| i_cal_request_status_from_num | add |
| i_cal_request_status_major | add |
| i_cal_request_status_minor | add |
| i_cal_time_as_ical_string | add |
| i_cal_time_clone | add |
| i_cal_time_convert_timezone | add |
| i_cal_time_convert_to_zone_inplace | add |
| i_cal_time_get_date | add |
| i_cal_time_get_day | add |
| i_cal_time_get_hour | add |
| i_cal_time_get_minute | add |
| i_cal_time_get_month | add |
| i_cal_time_get_second | add |
| i_cal_time_get_time | add |
| i_cal_time_get_type | add |
| i_cal_time_get_year | add |
| i_cal_time_is_daylight | add |
| i_cal_time_new | add |
| i_cal_time_new_current_with_zone | add |
| i_cal_time_new_from_day_of_year | add |
| i_cal_time_new_from_string | add |
| i_cal_time_new_from_timet_with_zone | add |
| i_cal_time_new_null_date | add |
| i_cal_time_new_today | add |
| i_cal_time_normalize_inplace | add |
| i_cal_time_set_date | add |
| i_cal_time_set_day | add |
| i_cal_time_set_hour | add |
| i_cal_time_set_is_date | add |
| i_cal_time_set_is_daylight | add |
| i_cal_time_set_minute | add |
| i_cal_time_set_month | add |
| i_cal_time_set_second | add |
| i_cal_time_set_time | add |
| i_cal_time_set_year | add |
| i_cal_time_span_clone | add |
| i_cal_time_span_get_is_busy | add |
| i_cal_time_span_new_timet | add |
| i_cal_time_timezone_expand_vtimezone | add |
| i_cal_trigger_get_duration | add |
| i_cal_trigger_get_time | add |
| i_cal_trigger_get_type | add |
| i_cal_trigger_is_bad_trigger | add |
| i_cal_trigger_is_null_trigger | add |
| i_cal_trigger_new_from_int | add |
| i_cal_trigger_new_from_string | add |
| i_cal_trigger_set_duration | add |
| i_cal_trigger_set_time | add |
| i_cal_value_as_ical_string | add |
| i_cal_value_clone | add |
| i_cal_value_kind_from_string | add |
| i_cal_value_kind_to_property_kind | add |
| i_cal_attach_get_data | change |
| i_cal_component_get_dtend | change |
| i_cal_component_get_dtstamp | change |
| i_cal_component_get_dtstart | change |
| i_cal_component_get_due | change |
| i_cal_component_get_duration | change |
| i_cal_component_get_recurrenceid | change |
| i_cal_component_set_dtend | change |
| i_cal_component_set_dtstamp | change |
| i_cal_component_set_dtstart | change |
| i_cal_component_set_due | change |
| i_cal_component_set_duration | change |
| i_cal_component_set_recurrenceid | change |
| i_cal_object_construct | change |
| i_cal_parser_get_line | change |
| i_cal_parser_parse | change |
| i_cal_property_get_acknowledged | change |
| i_cal_property_get_completed | change |
| i_cal_property_get_created | change |
| i_cal_property_get_datemax | change |
| i_cal_property_get_datemin | change |
| i_cal_property_get_dtend | change |
| i_cal_property_get_dtstamp | change |
| i_cal_property_get_dtstart | change |
| i_cal_property_get_due | change |
| i_cal_property_get_duration | change |
| i_cal_property_get_estimatedduration | change |
| i_cal_property_get_exdate | change |
| i_cal_property_get_exrule | change |
| i_cal_property_get_freebusy | change |
| i_cal_property_get_geo | change |
| i_cal_property_get_lastmodified | change |
| i_cal_property_get_maxdate | change |
| i_cal_property_get_mindate | change |
| i_cal_property_get_rdate | change |
| i_cal_property_get_recurrenceid | change |
| i_cal_property_get_requeststatus | change |
| i_cal_property_get_rrule | change |
| i_cal_property_get_trigger | change |
| i_cal_property_get_tzuntil | change |
| i_cal_property_new_acknowledged | change |
| i_cal_property_new_completed | change |
| i_cal_property_new_created | change |
| i_cal_property_new_datemax | change |
| i_cal_property_new_datemin | change |
| i_cal_property_new_dtend | change |
| i_cal_property_new_dtstamp | change |
| i_cal_property_new_dtstart | change |
| i_cal_property_new_due | change |
| i_cal_property_new_duration | change |
| i_cal_property_new_estimatedduration | change |
| i_cal_property_new_exdate | change |
| i_cal_property_new_exrule | change |
| i_cal_property_new_freebusy | change |
| i_cal_property_new_geo | change |
| i_cal_property_new_lastmodified | change |
| i_cal_property_new_maxdate | change |
| i_cal_property_new_mindate | change |
| i_cal_property_new_rdate | change |
| i_cal_property_new_recurrenceid | change |
| i_cal_property_new_requeststatus | change |
| i_cal_property_new_rrule | change |
| i_cal_property_new_trigger | change |
| i_cal_property_new_tzuntil | change |
| i_cal_property_set_acknowledged | change |
| i_cal_property_set_completed | change |
| i_cal_property_set_created | change |
| i_cal_property_set_datemax | change |
| i_cal_property_set_datemin | change |
| i_cal_property_set_dtend | change |
| i_cal_property_set_dtstamp | change |
| i_cal_property_set_dtstart | change |
| i_cal_property_set_due | change |
| i_cal_property_set_duration | change |
| i_cal_property_set_estimatedduration | change |
| i_cal_property_set_exdate | change |
| i_cal_property_set_exrule | change |
| i_cal_property_set_freebusy | change |
| i_cal_property_set_geo | change |
| i_cal_property_set_lastmodified | change |
| i_cal_property_set_maxdate | change |
| i_cal_property_set_mindate | change |
| i_cal_property_set_rdate | change |
| i_cal_property_set_recurrenceid | change |
| i_cal_property_set_requeststatus | change |
| i_cal_property_set_rrule | change |
| i_cal_property_set_trigger | change |
| i_cal_property_set_tzuntil | change |
| i_cal_recur_iterator_new | change |
| i_cal_recur_iterator_next | change |
| i_cal_recur_iterator_set_start | change |
| i_cal_time_add | change |
| i_cal_time_adjust | change |
| i_cal_time_as_timet | change |
| i_cal_time_compare | change |
| i_cal_time_compare_date_only | change |
| i_cal_time_compare_date_only_tz | change |
| i_cal_time_convert_to_zone | change |
| i_cal_time_day_of_week | change |
| i_cal_time_day_of_year | change |
| i_cal_time_normalize | change |
| i_cal_time_span_new | change |
| i_cal_time_start_doy_week | change |
| i_cal_time_subtract | change |
| i_cal_time_week_number | change |
| i_cal_timezone_get_utc_offset | change |
| i_cal_timezone_get_utc_offset_of_utc_time | change |
| i_cal_value_get_date | change |
| i_cal_value_get_datetime | change |
| i_cal_value_get_datetimedate | change |
| i_cal_value_get_datetimeperiod | change |
| i_cal_value_get_duration | change |
| i_cal_value_get_geo | change |
| i_cal_value_get_period | change |
| i_cal_value_get_recur | change |
| i_cal_value_get_requeststatus | change |
| i_cal_value_get_trigger | change |
| i_cal_value_new_date | change |
| i_cal_value_new_datetime | change |
| i_cal_value_new_datetimedate | change |
| i_cal_value_new_datetimeperiod | change |
| i_cal_value_new_duration | change |
| i_cal_value_new_geo | change |
| i_cal_value_new_period | change |
| i_cal_value_new_recur | change |
| i_cal_value_new_requeststatus | change |
| i_cal_value_new_trigger | change |
| i_cal_value_set_date | change |
| i_cal_value_set_datetime | change |
| i_cal_value_set_datetimedate | change |
| i_cal_value_set_datetimeperiod | change |
| i_cal_value_set_duration | change |
| i_cal_value_set_geo | change |
| i_cal_value_set_period | change |
| i_cal_value_set_recur | change |
| i_cal_value_set_requeststatus | change |
| i_cal_value_set_trigger | change |

## libid3tag 

For details, see [libid3tag](./LTS_to_SP2_changed_abi_detail/libid3tag_all_result.md\)

| function | type |
|:----  |:--  |
| id3_compat_fixup | add |
| id3_compat_lookup | add |
| id3_frametype_lookup | change |

## libiscsi

For details, see [libiscsi](./LTS_to_SP2_changed_abi_detail/libiscsi_all_result.md\)

| function | type |
|:----  |:--  |
| iscsi_extended_copy_sync | add |
| iscsi_extended_copy_task | add |
| iscsi_receive_copy_results_sync | add |
| iscsi_receive_copy_results_task | add |
| iscsi_compareandwrite_iov_sync | change |

## libksba 

For details, see [libksba](./LTS_to_SP2_changed_abi_detail/libksba_all_result.md\)

| function | type |
|:----  |:--  |
| ksba_der_add_bts | add |
| ksba_der_add_der | add |
| ksba_der_add_end | add |
| ksba_der_add_int | add |
| ksba_der_add_oid | add |
| ksba_der_add_ptr | add |
| ksba_der_add_tag | add |
| ksba_der_add_val | add |
| ksba_der_builder_get | add |
| ksba_der_builder_new | add |
| ksba_der_builder_reset | add |
| ksba_der_release | add |
| _ksba_keyinfo_from_sexp | change |

## libmpc 

For details, see [libmpc](./LTS_to_SP2_changed_abi_detail/libmpc_all_result.md\)

| function | type |
|:----  |:--  |
| mpc_dot | add |
| mpc_sum | add |

## libnet 

For details, see [libnet](./LTS_to_SP2_changed_abi_detail/libnet_all_result.md\)

| function | type |
|:----  |:--  |
| __libnet_print_vers | remove |
| libnet_build_ospfv2_hello_neighbor | add |
| libnet_adv_cull_header | change |

## libpsl 

For details, see [libpsl](./LTS_to_SP2_changed_abi_detail/libpsl_all_result.md\)

| function | type |
|:----  |:--  |
| psl_builtin | change |
| psl_free | change |
| psl_is_cookie_domain_acceptable | change |
| psl_is_public_suffix | change |
| psl_is_public_suffix2 | change |
| psl_latest | change |
| psl_load_file | change |
| psl_load_fp | change |
| psl_suffix_count | change |
| psl_suffix_exception_count | change |
| psl_suffix_wildcard_count | change |

## librepo 

For details, see [librepo](./LTS_to_SP2_changed_abi_detail/librepo_all_result.md\)

| function | type |
|:----  |:--  |
| ensure_socket_dir_exists | add |

## libsecret 

For details, see [libsecret](./LTS_to_SP2_changed_abi_detail/libsecret_all_result.md\)

| function | type |
|:----  |:--  |
| secret_backend_flags_get_type | add |
| secret_backend_get | add |
| secret_backend_get_finish | add |
| secret_backend_get_type | add |
| secret_file_backend_get_type | add |
| secret_file_collection_clear | add |
| secret_file_collection_get_type | add |
| secret_file_collection_replace | add |
| secret_file_collection_search | add |
| secret_file_collection_write | add |
| secret_file_collection_write_finish | add |
| secret_file_item_deserialize | add |
| secret_file_item_get_type | add |
| secret_file_item_serialize | add |
| secret_password_lookup_binary_finish | add |
| secret_password_lookup_binary_sync | add |
| secret_password_lookupv_binary_sync | add |
| secret_password_search | add |
| secret_password_search_finish | add |
| secret_password_search_sync | add |
| secret_password_searchv | add |
| secret_password_searchv_sync | add |
| secret_password_store_binary | add |
| secret_password_store_binary_sync | add |
| secret_password_storev_binary | add |
| secret_password_storev_binary_sync | add |
| secret_retrievable_get_attributes | add |
| secret_retrievable_get_created | add |
| secret_retrievable_get_label | add |
| secret_retrievable_get_modified | add |
| secret_retrievable_get_type | add |
| secret_retrievable_retrieve_secret | add |
| secret_retrievable_retrieve_secret_finish | add |
| secret_retrievable_retrieve_secret_sync | add |
| secret_value_unref_to_password | add |

## libvma

For details, see [libvma](./LTS_to_SP2_changed_abi_detail/libvma_all_result.md\)

| function | type |
|:----  |:--  |
| Floyd_LogCircleInfo | change |
| buffer_pool::find_lkey_by_ib_ctx_thread_safe | change |
| cq_mgr::add_qp_rx | change |
| sockinfo_tcp::accept_clone | change |
| vma_ib_mlx5dv_init_obj | change |

## libwebsockets 

For details, see [libwebsockets](./LTS_to_SP2_changed_abi_detail/libwebsockets_all_result.md\)

| function | type |
|:----  |:--  |
| interface_to_sa | remove |
| lws_alloc_vfs_file | remove |
| lws_client_connect | remove |
| lws_client_connect_extended | remove |
| lws_context_destroy2 | remove |
| lws_context_init_extensions | remove |
| lws_context_init_server_ssl | remove |
| lws_ext_parse_options | remove |
| lws_extension_callback_pm_deflate | remove |
| lws_plat_change_pollfd | remove |
| lws_plat_check_connection_error | remove |
| lws_plat_context_early_destroy | remove |
| lws_plat_context_early_init | remove |
| lws_plat_context_late_destroy | remove |
| lws_plat_delete_socket_from_fds | remove |
| lws_plat_drop_app_privileges | remove |
| lws_plat_inet_ntop | remove |
| lws_plat_inet_pton | remove |
| lws_plat_init | remove |
| lws_plat_insert_socket_into_fds | remove |
| lws_plat_service | remove |
| lws_plat_service_periodic | remove |
| lws_plat_set_socket_options | remove |
| lws_poll_listen_fd | remove |
| lws_read | remove |
| lws_server_get_canonical_hostname | remove |
| lws_server_socket_service | remove |
| lws_server_socket_service_ssl | remove |
| lws_set_parent_carries_io | remove |
| lws_ssl_capable_read | remove |
| lws_ssl_capable_read_no_ssl | remove |
| lws_ssl_capable_write | remove |
| lws_ssl_capable_write_no_ssl | remove |
| lws_ssl_close | remove |
| lws_ssl_destroy | remove |
| lws_ssl_pending | remove |
| lws_ssl_pending_no_ssl | remove |
| lws_union_transition | remove |
| lws_vhost_get | remove |
| __lws_sul_insert | add |
| __lws_sul_service_ripe | add |
| __lws_system_attach | add |
| lejp_change_callback | add |
| lejp_check_path_match | add |
| lejp_construct | add |
| lejp_destruct | add |
| lejp_error_to_string | add |
| lejp_get_wildcard | add |
| lejp_parse | add |
| lejp_parser_pop | add |
| lejp_parser_push | add |
| lws_add_http_common_headers | add |
| lws_adopt_descriptor_vhost_via_info | add |
| lws_b64_decode_state_init | add |
| lws_b64_decode_stateful | add |
| lws_b64_decode_string_len | add |
| lws_b64_encode_string_url | add |
| lws_base64_size | add |
| lws_buflist_append_segment | add |
| lws_buflist_describe | add |
| lws_buflist_destroy_all_segments | add |
| lws_buflist_linear_copy | add |
| lws_buflist_next_segment_len | add |
| lws_buflist_total_len | add |
| lws_buflist_use_segment | add |
| lws_callback_vhost_protocols_vhost | add |
| lws_client_http_multipart | add |
| lws_cmdline_option | add |
| lws_cmdline_option_handle_builtin | add |
| lws_create_adopt_udp | add |
| lws_dir | add |
| lws_diskcache_create | add |
| lws_diskcache_destroy | add |
| lws_diskcache_finalize_name | add |
| lws_diskcache_prepare | add |
| lws_diskcache_query | add |
| lws_diskcache_secs_to_idle | add |
| lws_diskcache_trim | add |
| lws_dll2_add_before | add |
| lws_dll2_add_head | add |
| lws_dll2_add_sorted | add |
| lws_dll2_add_tail | add |
| lws_dll2_clear | add |
| lws_dll2_foreach_safe | add |
| lws_dll2_owner_clear | add |
| lws_dll2_remove | add |
| lws_explicit_bzero | add |
| lws_filename_purify_inplace | add |
| lws_finalize_write_http_header | add |
| lws_fts_close | add |
| lws_fts_create | add |
| lws_fts_destroy | add |
| lws_fts_file_index | add |
| lws_fts_fill | add |
| lws_fts_open | add |
| lws_fts_search | add |
| lws_fts_serialize | add |
| lws_genaes_create | add |
| lws_genaes_crypt | add |
| lws_genaes_destroy | add |
| lws_gencrypto_bits_to_bytes | add |
| lws_gencrypto_jwe_alg_to_definition | add |
| lws_gencrypto_jwe_enc_to_definition | add |
| lws_gencrypto_jws_alg_to_definition | add |
| lws_gencrypto_padded_length | add |
| lws_genec_destroy | add |
| lws_genec_destroy_elements | add |
| lws_genec_dump | add |
| lws_genecdh_compute_shared_secret | add |
| lws_genecdh_create | add |
| lws_genecdh_new_keypair | add |
| lws_genecdh_set_key | add |
| lws_genecdsa_create | add |
| lws_genecdsa_hash_sig_verify_jws | add |
| lws_genecdsa_hash_sign_jws | add |
| lws_genecdsa_new_keypair | add |
| lws_genecdsa_set_key | add |
| lws_genhmac_destroy | add |
| lws_genhmac_init | add |
| lws_genhmac_size | add |
| lws_genhmac_update | add |
| lws_genrsa_create | add |
| lws_genrsa_destroy | add |
| lws_genrsa_destroy_elements | add |
| lws_genrsa_hash_sig_verify | add |
| lws_genrsa_hash_sign | add |
| lws_genrsa_new_keypair | add |
| lws_genrsa_private_decrypt | add |
| lws_genrsa_private_encrypt | add |
| lws_genrsa_public_decrypt | add |
| lws_genrsa_public_encrypt | add |
| lws_get_effective_uid_gid | add |
| lws_get_opaque_user_data | add |
| lws_get_peer_simple_fd | add |
| lws_get_tsi | add |
| lws_get_udp | add |
| lws_get_vhost_by_name | add |
| lws_get_vhost_iface | add |
| lws_get_vhost_listen_port | add |
| lws_get_vhost_name | add |
| lws_get_vhost_port | add |
| lws_get_vhost_user | add |
| lws_h2_client_stream_long_poll_rxonly | add |
| lws_h2_get_peer_txcredit_estimate | add |
| lws_h2_update_peer_txcredit | add |
| lws_hdr_custom_copy | add |
| lws_hdr_custom_length | add |
| lws_hex_to_byte_array | add |
| lws_http_basic_auth_gen | add |
| lws_http_compression_apply | add |
| lws_http_get_uri_and_method | add |
| lws_http_headers_detach | add |
| lws_http_is_redirected_to_get | add |
| lws_http_mark_sse | add |
| lws_humanize | add |
| lws_jose_destroy | add |
| lws_jose_init | add |
| lws_json_purify_len | add |
| lws_jwa_concat_kdf | add |
| lws_jwe_auth_and_decrypt | add |
| lws_jwe_auth_and_decrypt_cbc_hs | add |
| lws_jwe_be64 | add |
| lws_jwe_create_packet | add |
| lws_jwe_destroy | add |
| lws_jwe_encrypt | add |
| lws_jwe_init | add |
| lws_jwe_json_parse | add |
| lws_jwe_parse_jose | add |
| lws_jwe_render_compact | add |
| lws_jwe_render_flattened | add |
| lws_jwk_destroy | add |
| lws_jwk_dump | add |
| lws_jwk_dup_oct | add |
| lws_jwk_export | add |
| lws_jwk_generate | add |
| lws_jwk_import | add |
| lws_jwk_load | add |
| lws_jwk_rfc7638_fingerprint | add |
| lws_jwk_save | add |
| lws_jwk_strdup_meta | add |
| lws_jws_alloc_element | add |
| lws_jws_b64_compact_map | add |
| lws_jws_base64_enc | add |
| lws_jws_compact_decode | add |
| lws_jws_compact_encode | add |
| lws_jws_destroy | add |
| lws_jws_dup_element | add |
| lws_jws_encode_b64_element | add |
| lws_jws_encode_section | add |
| lws_jws_init | add |
| lws_jws_parse_jose | add |
| lws_jws_randomize_element | add |
| lws_jws_sig_confirm | add |
| lws_jws_sig_confirm_compact | add |
| lws_jws_sig_confirm_compact_b64 | add |
| lws_jws_sig_confirm_compact_b64_map | add |
| lws_jws_sig_confirm_json | add |
| lws_jws_sign_from_b64 | add |
| lws_jws_write_compact | add |
| lws_jws_write_flattened_json | add |
| lws_list_ptr_insert | add |
| lws_now_usecs | add |
| lws_open | add |
| lws_parse_numeric_address | add |
| lws_plat_read_file | add |
| lws_plat_recommended_rsa_bits | add |
| lws_plat_write_cert | add |
| lws_plat_write_file | add |
| lws_pvo_get_str | add |
| lws_pvo_search | add |
| lws_raw_transaction_completed | add |
| lws_retry_get_delay_ms | add |
| lws_retry_sul_schedule | add |
| lws_retry_sul_schedule_retry_wsi | add |
| lws_ring_dump | add |
| lws_sa46_compare_ads | add |
| lws_sa46_parse_numeric_address | add |
| lws_sa46_write_numeric_address | add |
| lws_seq_check_wsi | add |
| lws_seq_create | add |
| lws_seq_destroy | add |
| lws_seq_from_user | add |
| lws_seq_get_context | add |
| lws_seq_name | add |
| lws_seq_queue_event | add |
| lws_seq_timeout_us | add |
| lws_seq_us_since_creation | add |
| lws_ser_ru16be | add |
| lws_ser_ru32be | add |
| lws_ser_ru64be | add |
| lws_ser_wu16be | add |
| lws_ser_wu32be | add |
| lws_ser_wu64be | add |
| lws_set_opaque_user_data | add |
| lws_set_socks | add |
| lws_set_timer_usecs | add |
| lws_spa_create_via_info | add |
| lws_state_reg_deregister | add |
| lws_state_reg_notifier | add |
| lws_state_reg_notifier_list | add |
| lws_state_transition | add |
| lws_state_transition_steps | add |
| lws_strexp_expand | add |
| lws_strexp_init | add |
| lws_strexp_reset_out | add |
| lws_strncpy | add |
| lws_sul_schedule | add |
| lws_system_blob_destroy | add |
| lws_system_blob_direct_set | add |
| lws_system_blob_get | add |
| lws_system_blob_get_single_ptr | add |
| lws_system_blob_get_size | add |
| lws_system_blob_heap_append | add |
| lws_system_blob_heap_empty | add |
| lws_system_context_from_system_mgr | add |
| lws_system_get_blob | add |
| lws_system_get_ops | add |
| lws_system_get_state_manager | add |
| lws_threadpool_create | add |
| lws_threadpool_dequeue | add |
| lws_threadpool_destroy | add |
| lws_threadpool_dump | add |
| lws_threadpool_enqueue | add |
| lws_threadpool_finish | add |
| lws_threadpool_task_status_wsi | add |
| lws_threadpool_task_sync | add |
| lws_timed_callback_vh_protocol | add |
| lws_timed_callback_vh_protocol_us | add |
| lws_timingsafe_bcmp | add |
| lws_tls_acme_sni_cert_create | add |
| lws_tls_acme_sni_csr_create | add |
| lws_tls_cert_updated | add |
| lws_tls_client_vhost_extra_cert_mem | add |
| lws_tls_peer_cert_info | add |
| lws_tls_vhost_cert_info | add |
| lws_tokenize | add |
| lws_tokenize_cstr | add |
| lws_tokenize_init | add |
| lws_validity_confirmed | add |
| lws_vbi_decode | add |
| lws_vbi_encode | add |
| lws_write_numeric_address | add |
| lws_wsi_tx_credit | add |
| lws_x509_create | add |
| lws_x509_destroy | add |
| lws_x509_info | add |
| lws_x509_jwk_privkey_pem | add |
| lws_x509_parse_from_pem | add |
| lws_x509_public_to_jwk | add |
| lws_x509_verify | add |
| lwsac_align | add |
| lwsac_cached_file | add |
| lwsac_detach | add |
| lwsac_extend | add |
| lwsac_free | add |
| lwsac_get_next | add |
| lwsac_get_tail_pos | add |
| lwsac_info | add |
| lwsac_reference | add |
| lwsac_scan_extant | add |
| lwsac_sizeof | add |
| lwsac_total_alloc | add |
| lwsac_total_overhead | add |
| lwsac_unreference | add |
| lwsac_use | add |
| lwsac_use_backfill | add |
| lwsac_use_cached_file_detach | add |
| lwsac_use_cached_file_end | add |
| lwsac_use_cached_file_start | add |
| lwsac_use_zero | add |
| lwsl_emit_stderr_notimestamp | add |
| lwsws_get_config_globals | add |
| lwsws_get_config_vhosts | add |
| _lws_plat_service_tsi | change |
| lws_add_http_header_by_token | change |
| lws_adopt_descriptor_vhost | change |
| lws_chunked_html_process | change |
| lws_client_connect_via_info | change |
| lws_client_reset | change |
| lws_create_context | change |
| lws_create_vhost | change |
| lws_genhash_init | change |
| lws_genhash_size | change |
| lws_get_peer_simple | change |
| lws_get_peer_write_allowance | change |
| lws_get_random | change |
| lws_hdr_copy | change |
| lws_init_vhost_client_ssl | change |
| lws_ring_bump_head | change |
| lws_set_timeout | change |
| lws_spa_create | change |
| lws_token_to_string | change |

## libxslt

For details, see [libxslt](./LTS_to_SP2_changed_abi_detail/libxslt_all_result.md\)

| function | type |
|:----  |:--  |
| exsltDateXpathCtxtRegister | change |
| xsltCompMatchClearCache | add |
| xsltParseStylesheetUser | add |
| xslAddCall | change |
| xsltApplyImports | change |
| xsltApplyTemplates | change |
| xsltAttribute | change |
| xsltCallTemplate | change |
| xsltChoose | change |
| xsltComment | change |
| xsltCopy | change |
| xsltCopyOf | change |
| xsltDebug | change |
| xsltDocumentElem | change |
| xsltElement | change |
| xsltForEach | change |
| xsltIf | change |
| xsltNumber | change |
| xsltProcessingInstruction | change |
| xsltSort | change |
| xsltText | change |
| xsltValueOf | change |
| xsltXPathFunctionLookup | change |

## lm_sensors

For details, see [lm_sensors](./LTS_to_SP2_changed_abi_detail/lm_sensors_all_result.md\)

| function | type |
|:----  |:--  |
| None | None |

## nftables 

For details, see [nftables](./LTS_to_SP2_changed_abi_detail/nftables_all_result.md\)

| function | type |
|:----  |:--  |
| __memory_allocation_error | remove |
| __netlink_abi_error | remove |
| __netlink_init_error | remove |
| __stmt_binary_error | remove |
| alloc_nft_expr | remove |
| alloc_nftnl_chain | remove |
| alloc_nftnl_rule | remove |
| alloc_nftnl_set | remove |
| alloc_nftnl_table | remove |
| binop_expr_alloc | remove |
| bitmask_expr_to_binops | remove |
| cache_flush | remove |
| cache_release | remove |
| cache_update | remove |
| chain_add_hash | remove |
| chain_alloc | remove |
| chain_free | remove |
| chain_get | remove |
| chain_hookname_lookup | remove |
| chain_lookup | remove |
| chain_policy2str | remove |
| chain_print_plain | remove |
| chain_type_name_lookup | remove |
| cmd_alloc | remove |
| cmd_alloc_obj_ct | remove |
| cmd_evaluate | remove |
| cmd_free | remove |
| compound_expr_add | remove |
| compound_expr_alloc | remove |
| compound_expr_remove | remove |
| concat_expr_alloc | remove |
| concat_type_alloc | remove |
| concat_type_destroy | remove |
| connlimit_stmt_alloc | remove |
| constant_expr_alloc | remove |
| constant_expr_join | remove |
| constant_expr_splice | remove |
| counter_stmt_alloc | remove |
| ct_dir2str | remove |
| ct_expr_alloc | remove |
| ct_expr_update_type | remove |
| ct_label2str | remove |
| ct_label_table_exit | remove |
| ct_label_table_init | remove |
| ct_stmt_alloc | remove |
| data_unit_parse | remove |
| datatype_lookup | remove |
| datatype_lookup_byname | remove |
| datatype_print | remove |
| devgroup_table_exit | remove |
| devgroup_table_init | remove |
| do_command | remove |
| dup_stmt_alloc | remove |
| erec_add_location | remove |
| erec_create | remove |
| erec_destroy | remove |
| erec_print | remove |
| erec_print_list | remove |
| erec_vcreate | remove |
| expr_alloc | remove |
| expr_basetype | remove |
| expr_binary_error | remove |
| expr_clone | remove |
| expr_cmp | remove |
| expr_describe | remove |
| expr_free | remove |
| expr_get | remove |
| expr_print | remove |
| expr_set_type | remove |
| expr_stmt_alloc | remove |
| exthdr_dependency_kill | remove |
| exthdr_expr_alloc | remove |
| exthdr_find_proto | remove |
| exthdr_find_template | remove |
| exthdr_gen_dependency | remove |
| exthdr_init_raw | remove |
| exthdr_stmt_alloc | remove |
| family2str | remove |
| fib_expr_alloc | remove |
| fib_result_str | remove |
| flag_expr_alloc | remove |
| flow_offload_stmt_alloc | remove |
| flowtable_add_hash | remove |
| flowtable_alloc | remove |
| flowtable_free | remove |
| flowtable_get | remove |
| flowtable_print | remove |
| fwd_stmt_alloc | remove |
| get_rate | remove |
| get_set_decompose | remove |
| get_set_intervals | remove |
| get_unit | remove |
| gmp_init | remove |
| handle_free | remove |
| handle_merge | remove |
| hash_expr_alloc | remove |
| hooknum2str | remove |
| iface_cache_release | remove |
| iface_cache_update | remove |
| interval_map_decompose | remove |
| limit_stmt_alloc | remove |
| list_expr_alloc | remove |
| list_expr_sort | remove |
| log_level | remove |
| log_level_parse | remove |
| log_stmt_alloc | remove |
| map_expr_alloc | remove |
| map_stmt_alloc | remove |
| mapping_expr_alloc | remove |
| mark_table_exit | remove |
| mark_table_init | remove |
| markup_alloc | remove |
| markup_free | remove |
| meta_expr_alloc | remove |
| meta_key_parse | remove |
| meta_stmt_alloc | remove |
| meta_stmt_meta_iiftype | remove |
| meter_stmt_alloc | remove |
| mnl_batch_begin | remove |
| mnl_batch_end | remove |
| mnl_batch_init | remove |
| mnl_batch_ready | remove |
| mnl_batch_reset | remove |
| mnl_batch_talk | remove |
| mnl_err_list_free | remove |
| mnl_genid_get | remove |
| mnl_nft_chain_batch_add | remove |
| mnl_nft_chain_batch_del | remove |
| mnl_nft_chain_dump | remove |
| mnl_nft_event_listener | remove |
| mnl_nft_flowtable_batch_add | remove |
| mnl_nft_flowtable_batch_del | remove |
| mnl_nft_flowtable_dump | remove |
| mnl_nft_obj_batch_add | remove |
| mnl_nft_obj_batch_del | remove |
| mnl_nft_obj_dump | remove |
| mnl_nft_rule_batch_add | remove |
| mnl_nft_rule_batch_del | remove |
| mnl_nft_rule_batch_replace | remove |
| mnl_nft_rule_dump | remove |
| mnl_nft_ruleset_dump | remove |
| mnl_nft_set_batch_add | remove |
| mnl_nft_set_batch_del | remove |
| mnl_nft_set_dump | remove |
| mnl_nft_setelem_batch_add | remove |
| mnl_nft_setelem_batch_del | remove |
| mnl_nft_setelem_batch_flush | remove |
| mnl_nft_setelem_get | remove |
| mnl_nft_setelem_get_one | remove |
| mnl_nft_table_batch_add | remove |
| mnl_nft_table_batch_del | remove |
| mnl_nft_table_dump | remove |
| mnl_seqnum_alloc | remove |
| monitor_alloc | remove |
| monitor_free | remove |
| mpz_bitmask | remove |
| mpz_export_data | remove |
| mpz_get_be16 | remove |
| mpz_get_be32 | remove |
| mpz_get_uint16 | remove |
| mpz_get_uint32 | remove |
| mpz_get_uint64 | remove |
| mpz_get_uint8 | remove |
| mpz_import_data | remove |
| mpz_init_bitmask | remove |
| mpz_lshift_ui | remove |
| mpz_prefixmask | remove |
| mpz_rshift_ui | remove |
| mpz_switch_byteorder | remove |
| must_print_eq_op | remove |
| nat_etype2str | remove |
| nat_stmt_alloc | remove |
| netlink_add_chain_batch | remove |
| netlink_add_flowtable | remove |
| netlink_add_obj | remove |
| netlink_add_rule_batch | remove |
| netlink_add_set_batch | remove |
| netlink_add_setelems_batch | remove |
| netlink_add_table_batch | remove |
| netlink_alloc_data | remove |
| netlink_alloc_value | remove |
| netlink_batch_send | remove |
| netlink_close_sock | remove |
| netlink_del_rule_batch | remove |
| netlink_delete_chain_batch | remove |
| netlink_delete_flowtable | remove |
| netlink_delete_obj | remove |
| netlink_delete_set_batch | remove |
| netlink_delete_setelems_batch | remove |
| netlink_delete_table_batch | remove |
| netlink_delinearize_chain | remove |
| netlink_delinearize_obj | remove |
| netlink_delinearize_rule | remove |
| netlink_delinearize_set | remove |
| netlink_delinearize_setelem | remove |
| netlink_delinearize_table | remove |
| netlink_dump_chain | remove |
| netlink_dump_expr | remove |
| netlink_dump_obj | remove |
| netlink_dump_rule | remove |
| netlink_dump_ruleset | remove |
| netlink_dump_set | remove |
| netlink_echo_callback | remove |
| netlink_events_trace_cb | remove |
| netlink_flush_chain | remove |
| netlink_flush_setelems | remove |
| netlink_gen_data | remove |
| netlink_gen_raw_data | remove |
| netlink_genid_get | remove |
| netlink_get_setelem | remove |
| netlink_io_error | remove |
| netlink_linearize_rule | remove |
| netlink_list_chains | remove |
| netlink_list_flowtables | remove |
| netlink_list_objs | remove |
| netlink_list_setelems | remove |
| netlink_list_sets | remove |
| netlink_list_table | remove |
| netlink_list_tables | remove |
| netlink_markup_parse_cb | remove |
| netlink_monitor | remove |
| netlink_open_sock | remove |
| netlink_parse_set_expr | remove |
| netlink_rename_chain_batch | remove |
| netlink_replace_rule_batch | remove |
| netlink_reset_objs | remove |
| netlink_restart | remove |
| nft__create_buffer | remove |
| nft__delete_buffer | remove |
| nft__flush_buffer | remove |
| nft__scan_buffer | remove |
| nft__scan_bytes | remove |
| nft__scan_string | remove |
| nft__switch_to_buffer | remove |
| nft_alloc | remove |
| nft_cmd_expand | remove |
| nft_ctx_output_get_echo | remove |
| nft_ctx_output_get_handle | remove |
| nft_ctx_output_get_ip2name | remove |
| nft_ctx_output_get_json | remove |
| nft_ctx_output_get_numeric | remove |
| nft_ctx_output_get_stateless | remove |
| nft_ctx_output_set_echo | remove |
| nft_ctx_output_set_handle | remove |
| nft_ctx_output_set_ip2name | remove |
| nft_ctx_output_set_json | remove |
| nft_ctx_output_set_numeric | remove |
| nft_ctx_output_set_stateless | remove |
| nft_free | remove |
| nft_get_column | remove |
| nft_get_debug | remove |
| nft_get_extra | remove |
| nft_get_in | remove |
| nft_get_leng | remove |
| nft_get_lineno | remove |
| nft_get_lloc | remove |
| nft_get_lval | remove |
| nft_get_out | remove |
| nft_get_text | remove |
| nft_gmp_print | remove |
| nft_if_indextoname | remove |
| nft_if_nametoindex | remove |
| nft_lex | remove |
| nft_lex_destroy | remove |
| nft_lex_init | remove |
| nft_lex_init_extra | remove |
| nft_parse | remove |
| nft_pop_buffer_state | remove |
| nft_print | remove |
| nft_push_buffer_state | remove |
| nft_realloc | remove |
| nft_restart | remove |
| nft_set_column | remove |
| nft_set_debug | remove |
| nft_set_extra | remove |
| nft_set_in | remove |
| nft_set_lineno | remove |
| nft_set_lloc | remove |
| nft_set_lval | remove |
| nft_set_out | remove |
| notrack_stmt_alloc | remove |
| numgen_expr_alloc | remove |
| obj_add_hash | remove |
| obj_alloc | remove |
| obj_free | remove |
| obj_get | remove |
| obj_lookup | remove |
| obj_print | remove |
| obj_print_plain | remove |
| obj_type_name | remove |
| obj_type_to_cmd | remove |
| objref_stmt_alloc | remove |
| objref_type_name | remove |
| parser_init | remove |
| payload_can_merge | remove |
| payload_dependency_exists | remove |
| payload_dependency_kill | remove |
| payload_dependency_release | remove |
| payload_dependency_reset | remove |
| payload_dependency_store | remove |
| payload_expr_alloc | remove |
| payload_expr_complete | remove |
| payload_expr_expand | remove |
| payload_expr_join | remove |
| payload_expr_trim | remove |
| payload_gen_dependency | remove |
| payload_hdr_field | remove |
| payload_init_raw | remove |
| payload_is_known | remove |
| payload_is_stacked | remove |
| payload_stmt_alloc | remove |
| prefix_expr_alloc | remove |
| proto_ctx_init | remove |
| proto_ctx_update | remove |
| proto_dev_desc | remove |
| proto_dev_type | remove |
| proto_find_num | remove |
| proto_find_upper | remove |
| queue_stmt_alloc | remove |
| quota_stmt_alloc | remove |
| range_expr_alloc | remove |
| range_expr_value_high | remove |
| range_expr_value_low | remove |
| rate_parse | remove |
| rb_erase | remove |
| rb_first | remove |
| rb_insert_color | remove |
| rb_last | remove |
| rb_next | remove |
| rb_prev | remove |
| rb_replace_node | remove |
| realm_table_meta_exit | remove |
| realm_table_meta_init | remove |
| realm_table_rt_exit | remove |
| realm_table_rt_init | remove |
| reject_stmt_alloc | remove |
| relational_expr_alloc | remove |
| relational_expr_pctx_update | remove |
| rt_expr_alloc | remove |
| rt_expr_update_type | remove |
| rt_symbol_table_free | remove |
| rt_symbol_table_init | remove |
| rule_alloc | remove |
| rule_free | remove |
| rule_get | remove |
| rule_lookup | remove |
| rule_postprocess | remove |
| rule_print | remove |
| scanner_destroy | remove |
| scanner_include_file | remove |
| scanner_init | remove |
| scanner_push_buffer | remove |
| scanner_read_file | remove |
| scope_init | remove |
| scope_release | remove |
| set_add_hash | remove |
| set_alloc | remove |
| set_clone | remove |
| set_datatype_alloc | remove |
| set_datatype_destroy | remove |
| set_elem_expr_alloc | remove |
| set_expr_alloc | remove |
| set_free | remove |
| set_get | remove |
| set_lookup | remove |
| set_lookup_global | remove |
| set_policy2str | remove |
| set_print | remove |
| set_print_plain | remove |
| set_ref_expr_alloc | remove |
| set_stmt_alloc | remove |
| set_to_intervals | remove |
| socket_expr_alloc | remove |
| stmt_alloc | remove |
| stmt_evaluate | remove |
| stmt_free | remove |
| stmt_list_free | remove |
| stmt_print | remove |
| symbol_bind | remove |
| symbol_expr_alloc | remove |
| symbol_get | remove |
| symbol_lookup | remove |
| symbol_parse | remove |
| symbol_table_print | remove |
| symbol_unbind | remove |
| symbolic_constant_parse | remove |
| symbolic_constant_print | remove |
| table_add_hash | remove |
| table_alloc | remove |
| table_free | remove |
| table_get | remove |
| table_lookup | remove |
| tcpopt_expr_alloc | remove |
| tcpopt_find_template | remove |
| tcpopt_init_raw | remove |
| time_parse | remove |
| time_print | remove |
| unary_expr_alloc | remove |
| variable_expr_alloc | remove |
| verdict_expr_alloc | remove |
| verdict_stmt_alloc | remove |
| xfree | remove |
| xmalloc | remove |
| xmalloc_array | remove |
| xrealloc | remove |
| xstrdup | remove |
| xstrunescape | remove |
| xt_stmt_alloc | remove |
| xzalloc | remove |
| nft_ctx_output_get_flags | add |
| nft_ctx_output_set_flags | add |
| nft_ctx_add_include_path | change |
| nft_run_cmd_from_buffer | change |

## openhpi 

For details, see [openhpi](./LTS_to_SP2_changed_abi_detail/openhpi_all_result.md\)

| function | type |
|:----  |:--  |
| curlerr_to_ov_rest_err | change |

## OpenIPMI

For details, see [OpenIPMI](./LTS_to_SP2_changed_abi_detail/OpenIPMI_all_result.md\)

| function | type |
|:----  |:--  |
| sel_select_intr_sigmask | add |
| sel_setup_forked_process | add |
| sel_select_intr_sigmask | add |
| sel_setup_forked_process | add |
| ipmbserv_handle_data | add |
| ipmbserv_init | add |
| ipmbserv_read_config | add |
| chan_init | change |
| debug_log_raw_msg | change |
| handle_asf | change |
| ra_setup | change |

## openldap

For details, see [openldap](./LTS_to_SP2_changed_abi_detail/openldap_all_result.md\)

| function | type |
|:----  |:--  |
| ldap_abandon | change |
| ldap_int_initialize_global_options | change |
| ldap_int_sasl_config | change |
| ldap_int_tls_destroy | change |
| ldap_abandon | change |
| ldap_int_initialize_global_options | change |
| ldap_int_sasl_config | change |
| ldap_int_tls_destroy | change |

## pam 

For details, see [pam](./LTS_to_SP2_changed_abi_detail/pam_all_result.md\)

| function | type |
|:----  |:--  |
| pam_modutil_check_user_in_passwd | add |
| pam_modutil_search_key | add |
| pam_start_confdir | add |
| pam_acct_mgmt | change |
| pam_sm_authenticate | add |
| pam_sm_setcred | add |

## pkgconf 

For details, see [pkgconf](./LTS_to_SP2_changed_abi_detail/pkgconf_all_result.md\)

| function | type |
|:----  |:--  |
| pkgconf_client_dir_list_build | change |

## plymouth 

For details, see [plymouth](./LTS_to_SP2_changed_abi_detail/plymouth_all_result.md\)

| function | type |
|:----  |:--  |
| ply_text_progress_bar_get_percent_done | remove |
| ply_text_progress_bar_set_percent_done | remove |
| ply_text_step_bar_get_percent_done | remove |
| ply_text_step_bar_set_percent_done | remove |
| ply_text_progress_bar_get_fraction_done | add |
| ply_text_progress_bar_set_fraction_done | add |
| ply_text_step_bar_get_fraction_done | add |
| ply_text_step_bar_set_fraction_done | add |
| ply_renderer_backend_get_interface | change |
| ply_progress_animation_get_percent_done | remove |
| ply_progress_animation_set_percent_done | remove |
| ply_progress_bar_get_percent_done | remove |
| ply_progress_bar_set_percent_done | remove |
| ply_progress_animation_get_fraction_done | add |
| ply_progress_animation_set_fraction_done | add |
| ply_progress_bar_get_fraction_done | add |
| ply_progress_bar_set_fraction_done | add |
| ply_boot_splash_plugin_get_interface | change |

## readline 

For details, see [readline](./LTS_to_SP2_changed_abi_detail/readline_all_result.md\)

| function | type |
|:----  |:--  |
| _hs_history_patsearch | add |
| remove_history_range | add |

## rhash

For details, see [rhash](./LTS_to_SP2_changed_abi_detail/rhash_all_result.md\)

| function | type |
|:----  |:--  |
| rhash_torrent_add_file | change |
| rhash_torrent_get_default_piece_length | change |
| rhash_transmit | change |

## subversion 

For details, see [subversion](./LTS_to_SP2_changed_abi_detail/subversion_all_result.md\)

| function | type |
|:----  |:--  |
| svn_repos__dump_magic_header_record | add |
| svn_repos__dump_uuid_header_record | add |
| svn_repos__get_dump_editor | add |
| svn_repos_authz_parse2 | add |
| svn_repos_authz_read4 | add |
| svn_authz__parse | change |
| svn_client__copy_foreign | remove |
| svn_client_shelf_get_paths | remove |
| svn_client_shelf_has_changes | remove |
| svn_client_shelve | remove |
| svn_client_shelves_any | remove |
| svn_client_shelves_delete | remove |
| svn_client_shelves_list | remove |
| svn_client_unshelve | remove |
| svn_client__condense_commit_items2 | add |
| svn_client__get_diff_writer_svn | add |
| svn_client__layout_list | add |
| svn_client__repos_to_wc_copy_by_editor | add |
| svn_client__repos_to_wc_copy_internal | add |
| svn_client__shelf_apply | add |
| svn_client__shelf_close | add |
| svn_client__shelf_delete | add |
| svn_client__shelf_delete_newer_versions | add |
| svn_client__shelf_diff | add |
| svn_client__shelf_get_all_versions | add |
| svn_client__shelf_get_log_message | add |
| svn_client__shelf_get_newest_version | add |
| svn_client__shelf_list | add |
| svn_client__shelf_mods_editor | add |
| svn_client__shelf_open_existing | add |
| svn_client__shelf_open_or_create | add |
| svn_client__shelf_paths_changed | add |
| svn_client__shelf_replay | add |
| svn_client__shelf_revprop_get | add |
| svn_client__shelf_revprop_list | add |
| svn_client__shelf_revprop_set | add |
| svn_client__shelf_revprop_set_all | add |
| svn_client__shelf_save_new_version3 | add |
| svn_client__shelf_set_log_message | add |
| svn_client__shelf_test_apply_file | add |
| svn_client__shelf_unapply | add |
| svn_client__shelf_version_open | add |
| svn_client__shelf_version_status_walk | add |
| svn_client__wc_copy_mods | add |
| svn_client__wc_editor | add |
| svn_client__wc_editor_internal | add |
| svn_client__wc_replay | add |
| svn_client_blame6 | add |
| svn_client_conflict_option_get_moved_to_abspath_candidates2 | add |
| svn_client_conflict_option_get_moved_to_repos_relpath_candidates2 | add |
| svn_client_conflict_option_set_moved_to_abspath2 | add |
| svn_client_conflict_option_set_moved_to_repos_relpath2 | add |
| svn_client_diff7 | add |
| svn_client_diff_peg7 | add |
| svn_client_revert4 | add |
| svn_client__arbitrary_nodes_diff | change |
| svn_diff_hunk__create_adds_single_line | change |
| svn_relpath__internal_style | remove |
| svn_dirent_canonicalize_safe | add |
| svn_dirent_internal_style_safe | add |
| svn_opt_get_canonical_subcommand3 | add |
| svn_opt_get_option_from_code3 | add |
| svn_opt_print_generic_help3 | add |
| svn_opt_print_help5 | add |
| svn_opt_subcommand_help4 | add |
| svn_opt_subcommand_takes_option4 | add |
| svn_relpath__make_internal | add |
| svn_relpath_canonicalize_safe | add |
| svn_uri_canonicalize_safe | add |
| svn_delta_path_driver3 | add |
| svn_delta_path_driver_finish | add |
| svn_delta_path_driver_start | add |
| svn_delta_path_driver_step | add |
| svn_element__tree_set | change |
| svn_wc__find_repos_node_in_wc | remove |
| svn_wc__get_shelves_dir | remove |
| svn_wc__db_find_copies_of_repos_path | add |
| svn_wc__db_find_repos_node_in_wc | add |
| svn_wc__db_find_working_nodes_with_basename | add |
| svn_wc__find_copies_of_repos_path | add |
| svn_wc__find_working_nodes_with_basename | add |
| svn_wc__get_experimental_dir | add |
| svn_wc_revert6 | add |
| svn_wc__conflict_read_tree_conflict | change |
| svn_wc__conflict_skel_add_tree_conflict | change |
| svn_wc__diff7 | change |

## tcl 

For details, see [tcl](./LTS_to_SP2_changed_abi_detail/tcl_all_result.md\)

| function | type |
|:----  |:--  |
| TclOODefineMixinObjCmd | remove |
| TclSkipUnlink | remove |
| Tcl_EncodingObjCmd | remove |
| TclpUnloadFile | remove |
| TclBN_mp_balance_mul | add |
| TclBN_mp_expt_d_ex | add |
| TclBN_mp_set_ull | add |
| TclBN_mp_signed_rsh | add |
| TclBN_mp_to_radix | add |
| TclBN_mp_to_ubin | add |
| TclAddLiteralObj | change |
| TclBN_mp_unsigned_bin_size | change |

## xorg-x11-server

For details, see [xorg-x11-server](./LTS_to_SP2_changed_abi_detail/xorg-x11-server_all_result.md\)

| function | type |
|:----  |:--  |
| glamor_clear_pixmap | add |
| glamor_egl_get_driver_name | add |

## zstd 

For details, see [zstd](./LTS_to_SP2_changed_abi_detail/zstd_all_result.md\)

| function | type |
|:----  |:--  |
| ZSTDMT_compressCCtx | remove |
| ZSTDMT_compressStream | remove |
| ZSTDMT_compressStream_generic | remove |
| ZSTDMT_compress_advanced | remove |
| ZSTDMT_createCCtx | remove |
| ZSTDMT_createCCtx_advanced | remove |
| ZSTDMT_endStream | remove |
| ZSTDMT_flushStream | remove |
| ZSTDMT_freeCCtx | remove |
| ZSTDMT_getMTCtxParameter | remove |
| ZSTDMT_initCStream | remove |
| ZSTDMT_initCStream_advanced | remove |
| ZSTDMT_initCStream_usingCDict | remove |
| ZSTDMT_resetCStream | remove |
| ZSTDMT_setMTCtxParameter | remove |
| ZSTDMT_sizeof_CCtx | remove |
| ZSTD_CCtxParam_getParameter | remove |
| ZSTD_CCtxParam_setParameter | remove |
| ZSTD_CCtx_resetParameters | remove |
| ZSTD_compress_generic | remove |
| ZSTD_compress_generic_simpleArgs | remove |
| ZSTD_decompress_generic | remove |
| ZSTD_decompress_generic_simpleArgs | remove |
| ZSTD_setDStreamParameter | remove |

