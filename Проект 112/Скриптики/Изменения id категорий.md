Communal
`BEGIN; UPDATE communal_communal SET group_id = 1 WHERE group_id = 5; UPDATE communal_communal SET group_level_two_id = 1 WHERE group_level_two_id = 5; COMMIT;`

communal_communalpreview
`BEGIN; UPDATE communal_communalpreview SET group_id = 1 WHERE group_id = 5; UPDATE communal_communalpreview SET group_level_two_id = 1 WHERE group_level_two_id = 5; COMMIT;`

broadcast_broadcastsubscription_groups - 
`BEGIN; UPDATE broadcast_broadcastsubscription_groups SET broadcastgroup_id = 1 WHERE broadcastgroup_id = 5; COMMIT;`

broadcast_broadcast - 
`BEGIN; UPDATE broadcast_broadcast SET group_id = 1 WHERE group_id = 5; UPDATE broadcast_broadcast SET group_level_two_id = 1 WHERE group_level_two_id = 5; COMMIT;`

broadcast_broadcastgroup
`UPDATE broadcast_broadcast SET group_id = 1 WHERE group_id = 5; UPDATE broadcast_broadcast SET group_level_two_id = 1 WHERE group_level_two_id = 5; COMMIT;`