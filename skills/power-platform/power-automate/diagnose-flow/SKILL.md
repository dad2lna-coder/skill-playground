---
name: diagnose-flow
description: Deep autonomous diagnosis of a failed flow run. Provide environment, flow, and run IDs. Use when the user asks to diagnose or deeply investigate a specific failed flow run.
user-invocable: true
argument-hint: "<env-id> <flow-id> <run-id>"
context: fork
allowed-tools: Bash, Read, Write, Glob, Grep, mcp__flowagent__list_environments, mcp__flowagent__set_current_env, mcp__flowagent__get_current_env, mcp__flowagent__resolve_environment, mcp__flowagent__list_flows, mcp__flowagent__get_flow, mcp__flowagent__create_flow, mcp__flowagent__update_flow, mcp__flowagent__edit_flow, mcp__flowagent__copy_flow, mcp__flowagent__publish_flow, mcp__flowagent__disable_flow, mcp__flowagent__delete_flow, mcp__flowagent__run_flow, mcp__flowagent__get_run_history, mcp__flowagent__get_run_details, mcp__flowagent__get_run_actions, mcp__flowagent__get_run_action_content, mcp__flowagent__get_run_action_repetitions, mcp__flowagent__cancel_run, mcp__flowagent__cancel_all_runs, mcp__flowagent__resubmit_run, mcp__flowagent__diagnose_run, mcp__flowagent__list_connections, mcp__flowagent__list_connectors, mcp__flowagent__get_connector, mcp__flowagent__search_operations, mcp__flowagent__get_operation_details, mcp__flowagent__pick_or_create_connection, mcp__flowagent__resolve_refs, mcp__flowagent__resolve_params, mcp__flowagent__scaffold_flow, mcp__flowagent__list_templates, mcp__flowagent__validate_flow, mcp__flowagent__preflight_flow, mcp__flowagent__smoke_test, mcp__flowagent__get_expression_help, mcp__flowagent__list_desktop_flows, mcp__flowagent__list_machine_groups, mcp__flowagent__run_desktop_flow, mcp__flowagent__get_flow_context, mcp__flowagent__set_current_flow, mcp__flowagent__clear_current_flow, mcp__flowagent__invoke_operation, mcp__flowagent__get_past_trigger_inputs, mcp__flowagent__test_connection, mcp__flowagent__fix_connection, mcp__flowagent__delete_connection, mcp__flowagent__preview_update, mcp__flowagent__get_backup, mcp__flowagent__list_backups, mcp__flowagent__restore_backup, mcp__flowagent__list_trigger_emulators, mcp__flowagent__whoami, mcp__flowagent__doctor, mcp__flowagent__reconnect, mcp__flowagent__list_accounts, mcp__flowagent__switch_account
model: opus
---

# Diagnose Flow Run

Deep autonomous diagnosis of a failed flow run. Provide environment, flow, and run IDs.

**Workflow: [diagnose-flow-workflow.md](${PLUGIN_ROOT}/skills/diagnose-flow/diagnose-flow-workflow.md)** — Read and follow all phases defined in that bundled file.
