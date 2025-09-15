# Operational Runbooks & SRE Playbooks
## AI Departments Platform

**Version:** 1.0  
**Date:** 2025-09-13  
**Owner:** Felipe PM + Claude  
**Status:** APPROVED  

---

## Runbook Framework Overview

### Purpose & Scope
This document provides comprehensive operational runbooks for the AI Departments Platform, enabling reliable 24/7 operations with cost-efficient practices. These playbooks are designed for Brazilian micro-entrepreneurs' needs, ensuring high availability while maintaining cost consciousness.

### Operational Philosophy
- **Cost-First Operations**: Optimize for efficiency without compromising reliability
- **Proactive Monitoring**: Prevent issues before they impact customers
- **Automated Recovery**: Minimize manual intervention and human error
- **Clear Escalation**: Structured response for complex situations
- **Customer-Centric**: Focus on business impact to micro-entrepreneurs

### SRE Principles
- **99.5% Uptime Target**: Balanced availability for cost-conscious operations
- **Mean Time to Detection (MTTD)**: < 5 minutes for critical issues
- **Mean Time to Resolution (MTTR)**: < 30 minutes for P1 incidents
- **Error Budget**: 0.5% monthly downtime allowance
- **Automation First**: Reduce toil and operational overhead

---

## Incident Response Playbooks

### Incident Classification & Severity Levels
```yaml
incident_severity_matrix:
  P1_critical:
    definition: "Complete service outage or data security breach"
    examples:
      - platform_completely_inaccessible
      - payment_processing_failure
      - data_breach_detected
      - whatsapp_integration_down
    response_time: "immediate"
    escalation_trigger: "5_minutes"
    notification_targets: ["cto", "ceo", "security_team", "customer_support"]
    
  P2_high:
    definition: "Major feature degradation affecting multiple customers"
    examples:
      - ai_content_generation_slow_or_failing
      - authentication_issues
      - dashboard_loading_problems
      - integration_partial_outage
    response_time: "15_minutes"
    escalation_trigger: "30_minutes"
    notification_targets: ["engineering_team", "product_manager"]
    
  P3_medium:
    definition: "Minor feature issues or single customer impact"
    examples:
      - specific_template_not_working
      - minor_ui_glitches
      - reporting_delays
      - individual_integration_issues
    response_time: "2_hours"
    escalation_trigger: "4_hours"
    notification_targets: ["on_call_engineer"]
    
  P4_low:
    definition: "Cosmetic issues or enhancement requests"
    examples:
      - ui_inconsistencies
      - documentation_updates
      - feature_requests
      - performance_optimization_opportunities
    response_time: "next_business_day"
    escalation_trigger: "1_week"
    notification_targets: ["engineering_backlog"]
```

### P1 Critical Incident Response
```bash
#!/bin/bash
# P1 Critical Incident Response Script

# Immediate Response (0-5 minutes)
echo "P1 CRITICAL INCIDENT DETECTED"
echo "Timestamp: $(date)"
echo "Incident ID: $(uuidgen)"

# 1. Acknowledge and assess
curl -X POST "https://api.pagerduty.com/incidents" \
  -H "Authorization: Token ${PAGERDUTY_TOKEN}" \
  -d '{
    "incident": {
      "type": "incident",
      "title": "P1 Critical: Platform Outage",
      "service": {"id": "'"${SERVICE_ID}"'", "type": "service_reference"},
      "urgency": "high",
      "body": {"type": "incident_body", "details": "Critical system outage detected"}
    }
  }'

# 2. Create incident war room
python3 << EOF
import slack_sdk
client = slack_sdk.WebClient(token="${SLACK_BOT_TOKEN}")
response = client.conversations_create(
    name=f"incident-{incident_id}",
    is_private=False
)
war_room_id = response["channel"]["id"]

# Invite key stakeholders
stakeholders = ["felipe", "security-team", "engineering-team"]
for user in stakeholders:
    client.conversations_invite(channel=war_room_id, users=user)

# Post initial assessment
client.chat_postMessage(
    channel=war_room_id,
    text="🚨 P1 Critical Incident Declared - Initial Response Protocol Activated"
)
EOF

# 3. Immediate triage
echo "Running immediate system health checks..."

# Check core services
kubectl get pods -n production | grep -E "(api|web|worker)" | grep -v Running && echo "⚠️ Pod issues detected"

# Check database connectivity
pg_isready -h $DB_HOST -p $DB_PORT -U $DB_USER && echo "✅ Database accessible" || echo "❌ Database connection failed"

# Check Redis
redis-cli -h $REDIS_HOST -p $REDIS_PORT ping && echo "✅ Redis accessible" || echo "❌ Redis connection failed"

# Check external integrations
curl -f -s "https://graph.facebook.com/v18.0/me?access_token=${WHATSAPP_TOKEN}" > /dev/null && echo "✅ WhatsApp API accessible" || echo "❌ WhatsApp API failed"

# 4. Customer communication
python3 << EOF
# Post status page update
import requests
status_update = {
    "status_page_id": "${STATUS_PAGE_ID}",
    "incident": {
        "name": "Service Disruption - Investigating",
        "status": "investigating",
        "impact_override": "major",
        "body": "We're investigating reports of service disruption and will provide updates as we learn more."
    }
}
requests.post(
    f"https://api.statuspage.io/v1/pages/{status_page_id}/incidents",
    headers={"Authorization": f"OAuth {STATUS_PAGE_TOKEN}"},
    json=status_update
)
EOF

echo "P1 Critical incident response initiated - proceeding with detailed investigation"
```

### Service Recovery Procedures
```python
# Automated Service Recovery Script
import asyncio
import logging
from datetime import datetime, timedelta

class ServiceRecoveryOrchestrator:
    def __init__(self):
        self.logger = logging.getLogger(__name__)
        self.recovery_steps = []
        self.rollback_plan = []
        
    async def execute_recovery_procedure(self, incident_type: str, severity: str):
        """Execute automated recovery based on incident type"""
        
        recovery_start = datetime.utcnow()
        self.logger.info(f"Starting recovery for {incident_type} at {recovery_start}")
        
        try:
            if incident_type == "database_connection_failure":
                await self.recover_database_connectivity()
            elif incident_type == "api_service_outage":
                await self.recover_api_services()
            elif incident_type == "ai_service_degradation":
                await self.recover_ai_services()
            elif incident_type == "integration_failure":
                await self.recover_external_integrations()
            else:
                await self.generic_service_recovery()
                
            recovery_duration = datetime.utcnow() - recovery_start
            self.logger.info(f"Recovery completed in {recovery_duration.total_seconds():.2f} seconds")
            
        except Exception as e:
            self.logger.error(f"Recovery failed: {str(e)}")
            await self.initiate_rollback()
            raise
    
    async def recover_database_connectivity(self):
        """Recover database connectivity issues"""
        
        # Step 1: Check connection pool status
        await self.check_and_reset_connection_pools()
        
        # Step 2: Restart database connections
        await self.restart_database_connections()
        
        # Step 3: Verify read/write capability
        await self.verify_database_operations()
        
        # Step 4: Scale up database resources if needed
        if await self.detect_resource_constraints():
            await self.scale_database_resources()
    
    async def recover_api_services(self):
        """Recover API service outages"""
        
        # Step 1: Check service health
        unhealthy_services = await self.identify_unhealthy_services()
        
        # Step 2: Restart unhealthy pods
        for service in unhealthy_services:
            await self.restart_kubernetes_deployment(service)
            await asyncio.sleep(10)  # Allow time for startup
            
        # Step 3: Verify service endpoints
        await self.verify_api_endpoints()
        
        # Step 4: Check load balancer configuration
        await self.verify_load_balancer_health()
    
    async def recover_ai_services(self):
        """Recover AI service degradation"""
        
        # Step 1: Check AI provider connectivity
        providers = ["openai", "google", "anthropic"]
        for provider in providers:
            if not await self.check_ai_provider_health(provider):
                await self.route_traffic_away_from_provider(provider)
                
        # Step 2: Scale AI worker processes
        await self.scale_ai_workers()
        
        # Step 3: Clear AI request queues if backed up
        await self.clear_stale_ai_requests()
        
        # Step 4: Failover to backup AI models
        await self.activate_backup_ai_models()
```

---

## Monitoring & Alerting Playbooks

### Health Check Procedures
```python
# Comprehensive Health Check System
class HealthCheckOrchestrator:
    def __init__(self):
        self.checks = {
            "critical": [],
            "important": [],
            "informational": []
        }
        self.alert_thresholds = {
            "response_time": 2.0,  # seconds
            "error_rate": 0.05,    # 5%
            "cpu_usage": 0.80,     # 80%
            "memory_usage": 0.85,   # 85%
            "disk_usage": 0.90     # 90%
        }
        
    async def run_comprehensive_health_check(self) -> HealthCheckReport:
        """Run all health checks and generate comprehensive report"""
        
        report = HealthCheckReport(timestamp=datetime.utcnow())
        
        # Critical health checks (must pass for service to be operational)
        critical_results = await asyncio.gather(
            self.check_api_endpoints(),
            self.check_database_connectivity(),
            self.check_redis_connectivity(),
            self.check_ai_service_availability(),
            self.check_payment_processing(),
            self.check_whatsapp_integration()
        )
        
        # Important health checks (degradation indicators)
        important_results = await asyncio.gather(
            self.check_response_times(),
            self.check_error_rates(),
            self.check_queue_depths(),
            self.check_integration_latencies(),
            self.check_ssl_certificates()
        )
        
        # Informational checks (optimization opportunities)
        info_results = await asyncio.gather(
            self.check_resource_utilization(),
            self.check_cost_efficiency(),
            self.check_security_posture(),
            self.check_compliance_status()
        )
        
        # Compile results
        report.critical_checks = critical_results
        report.important_checks = important_results
        report.informational_checks = info_results
        
        # Determine overall health score
        report.overall_health_score = self.calculate_health_score(
            critical_results, important_results, info_results
        )
        
        # Generate alerts if needed
        if report.overall_health_score < 0.8:
            await self.generate_health_alerts(report)
            
        return report
    
    async def check_ai_service_availability(self) -> HealthCheckResult:
        """Check AI service providers availability and performance"""
        
        ai_checks = {}
        
        # Test Gemini 2.5 Flash (our primary model)
        try:
            start_time = time.time()
            response = await self.test_gemini_generation(
                "Test prompt for health check", max_tokens=50
            )
            response_time = time.time() - start_time
            
            ai_checks["gemini_2.5_flash"] = HealthCheckResult(
                service="gemini_2.5_flash",
                status="healthy" if response_time < 3.0 else "degraded",
                response_time=response_time,
                details={"tokens_generated": len(response.split())}
            )
        except Exception as e:
            ai_checks["gemini_2.5_flash"] = HealthCheckResult(
                service="gemini_2.5_flash",
                status="unhealthy",
                error=str(e)
            )
        
        # Test backup models
        for model in ["gpt-3.5-turbo", "gpt-4-turbo"]:
            try:
                start_time = time.time()
                response = await self.test_openai_generation(model, "Health check")
                response_time = time.time() - start_time
                
                ai_checks[model] = HealthCheckResult(
                    service=model,
                    status="healthy" if response_time < 5.0 else "degraded",
                    response_time=response_time
                )
            except Exception as e:
                ai_checks[model] = HealthCheckResult(
                    service=model,
                    status="unhealthy",
                    error=str(e)
                )
        
        # Calculate overall AI health
        healthy_services = sum(1 for check in ai_checks.values() if check.status == "healthy")
        total_services = len(ai_checks)
        
        return HealthCheckResult(
            service="ai_services_overall",
            status="healthy" if healthy_services >= 1 else "critical",
            details={
                "healthy_services": healthy_services,
                "total_services": total_services,
                "service_details": ai_checks
            }
        )
```

### Cost Monitoring Playbooks
```yaml
cost_monitoring_procedures:
  daily_cost_check:
    schedule: "every_day_at_6am_utc"
    thresholds:
      daily_budget_warning: "80_percent_of_daily_limit"
      daily_budget_critical: "95_percent_of_daily_limit"
      cost_spike_detection: "50_percent_increase_over_7_day_average"
      
    automated_actions:
      budget_80_percent:
        - send_slack_alert_to_finance_channel
        - email_notification_to_admin
        - log_cost_analysis_details
        
      budget_95_percent:
        - escalate_to_leadership_team
        - trigger_cost_optimization_review
        - prepare_emergency_cost_reduction_plan
        
      cost_spike_detected:
        - immediate_investigation_workflow
        - identify_cost_spike_source
        - implement_temporary_cost_controls
        
  ai_model_cost_optimization:
    monitoring_frequency: "hourly"
    cost_efficiency_targets:
      gemini_flash_usage: "minimum_80_percent_of_requests"
      gpt35_usage: "maximum_15_percent_of_requests"
      gpt4_usage: "maximum_5_percent_of_requests"
      
    optimization_triggers:
      high_cost_model_overuse:
        action: "review_routing_logic"
        escalation: "product_team_review"
        
      quality_degradation:
        action: "increase_model_tier_for_affected_tasks"
        monitoring: "track_customer_satisfaction_impact"
        
  infrastructure_cost_management:
    resource_utilization_targets:
      cpu_utilization: "70_to_85_percent_range"
      memory_utilization: "75_to_90_percent_range"
      storage_utilization: "maximum_80_percent"
      
    scaling_policies:
      scale_up_triggers:
        - cpu_above_85_percent_for_10_minutes
        - memory_above_90_percent_for_5_minutes
        - response_time_above_2_seconds_for_5_minutes
        
      scale_down_triggers:
        - cpu_below_40_percent_for_30_minutes
        - low_traffic_periods_identified
        - cost_optimization_opportunities_detected
```

---

## Backup & Recovery Procedures

### Database Backup & Restore
```bash
#!/bin/bash
# Database Backup and Recovery Procedures

# Automated Daily Backup
perform_daily_backup() {
    echo "Starting daily database backup: $(date)"
    
    # Create timestamped backup
    BACKUP_DATE=$(date +%Y%m%d_%H%M%S)
    BACKUP_FILE="aidepartments_backup_${BACKUP_DATE}.sql"
    
    # Perform backup with compression
    pg_dump -h $DB_HOST -U $DB_USER -d $DB_NAME \
        --no-password --verbose --format=custom \
        --compress=6 --file="/backups/${BACKUP_FILE}"
    
    if [ $? -eq 0 ]; then
        echo "✅ Database backup successful: ${BACKUP_FILE}"
        
        # Upload to secure cloud storage
        gsutil cp "/backups/${BACKUP_FILE}" \
            "gs://aidepartments-backups/database/${BACKUP_FILE}"
        
        # Encrypt backup with customer keys
        gpg --cipher-algo AES256 --compress-algo 1 --s2k-mode 3 \
            --s2k-digest-algo SHA512 --s2k-count 65536 \
            --symmetric --output "/backups/${BACKUP_FILE}.gpg" \
            "/backups/${BACKUP_FILE}"
        
        # Verify backup integrity
        pg_restore --list "/backups/${BACKUP_FILE}" > /dev/null
        if [ $? -eq 0 ]; then
            echo "✅ Backup integrity verified"
            
            # Clean up local unencrypted backup
            rm "/backups/${BACKUP_FILE}"
        else
            echo "❌ Backup integrity check failed"
            exit 1
        fi
    else
        echo "❌ Database backup failed"
        exit 1
    fi
}

# Point-in-Time Recovery
perform_point_in_time_recovery() {
    RECOVERY_TARGET_TIME="$1"
    
    if [ -z "$RECOVERY_TARGET_TIME" ]; then
        echo "Usage: perform_point_in_time_recovery 'YYYY-MM-DD HH:MM:SS'"
        exit 1
    fi
    
    echo "🔄 Starting point-in-time recovery to: $RECOVERY_TARGET_TIME"
    
    # Stop application services
    kubectl scale deployment api-service --replicas=0 -n production
    kubectl scale deployment worker-service --replicas=0 -n production
    
    # Create recovery database
    createdb -h $DB_HOST -U $DB_USER "${DB_NAME}_recovery"
    
    # Restore from latest backup before target time
    BACKUP_FILE=$(find_latest_backup_before_time "$RECOVERY_TARGET_TIME")
    
    pg_restore -h $DB_HOST -U $DB_USER -d "${DB_NAME}_recovery" \
        --no-owner --no-privileges --verbose "$BACKUP_FILE"
    
    # Apply WAL files for point-in-time recovery
    pg_ctl stop -D $PGDATA
    
    # Configure recovery
    cat > $PGDATA/recovery.conf << EOF
restore_command = 'cp /var/lib/postgresql/wal_archive/%f %p'
recovery_target_time = '$RECOVERY_TARGET_TIME'
recovery_target_inclusive = false
EOF
    
    # Start PostgreSQL in recovery mode
    pg_ctl start -D $PGDATA
    
    # Wait for recovery completion
    while [ ! -f "$PGDATA/recovery.done" ]; do
        echo "Recovery in progress..."
        sleep 30
    done
    
    echo "✅ Point-in-time recovery completed"
    
    # Verify data integrity
    verify_data_integrity "${DB_NAME}_recovery"
    
    # Switch to recovery database if verification passes
    if [ $? -eq 0 ]; then
        echo "Switching to recovered database..."
        switch_to_recovery_database
        
        # Restart application services
        kubectl scale deployment api-service --replicas=3 -n production
        kubectl scale deployment worker-service --replicas=2 -n production
        
        echo "✅ Recovery completed successfully"
    else
        echo "❌ Data integrity verification failed"
        exit 1
    fi
}
```

### File Storage Backup
```python
# Cloud Storage Backup and Recovery
import os
import asyncio
from google.cloud import storage
from datetime import datetime, timedelta

class FileStorageBackupManager:
    def __init__(self):
        self.storage_client = storage.Client()
        self.source_bucket = "aidepartments-storage"
        self.backup_bucket = "aidepartments-backups"
        self.retention_days = 90
        
    async def perform_incremental_backup(self):
        """Perform incremental backup of user files"""
        
        backup_start = datetime.utcnow()
        backup_session_id = f"backup_{backup_start.strftime('%Y%m%d_%H%M%S')}"
        
        try:
            # Get list of files modified since last backup
            last_backup_time = await self.get_last_backup_time()
            modified_files = await self.get_modified_files_since(last_backup_time)
            
            backup_manifest = {
                "backup_session_id": backup_session_id,
                "start_time": backup_start.isoformat(),
                "source_bucket": self.source_bucket,
                "files_to_backup": len(modified_files),
                "backup_type": "incremental"
            }
            
            # Backup modified files
            backed_up_files = []
            for file_path in modified_files:
                try:
                    backup_path = f"incremental/{backup_session_id}/{file_path}"
                    await self.backup_single_file(file_path, backup_path)
                    backed_up_files.append({
                        "original_path": file_path,
                        "backup_path": backup_path,
                        "backup_time": datetime.utcnow().isoformat()
                    })
                except Exception as e:
                    print(f"Failed to backup {file_path}: {str(e)}")
            
            # Create backup manifest
            backup_manifest.update({
                "end_time": datetime.utcnow().isoformat(),
                "files_backed_up": len(backed_up_files),
                "backed_up_files": backed_up_files,
                "status": "completed"
            })
            
            # Save manifest
            await self.save_backup_manifest(backup_session_id, backup_manifest)
            
            # Clean up old backups
            await self.cleanup_old_backups()
            
            print(f"✅ Incremental backup completed: {len(backed_up_files)} files backed up")
            return backup_manifest
            
        except Exception as e:
            print(f"❌ Incremental backup failed: {str(e)}")
            raise
    
    async def restore_files_from_backup(
        self, 
        restore_point: str, 
        file_patterns: list = None
    ):
        """Restore files from backup to specific point in time"""
        
        print(f"🔄 Starting file restore to point: {restore_point}")
        
        try:
            # Find appropriate backup session
            backup_session = await self.find_backup_session_for_restore_point(restore_point)
            
            if not backup_session:
                raise ValueError(f"No backup found for restore point: {restore_point}")
            
            # Get list of files to restore
            files_to_restore = await self.get_files_for_restore(
                backup_session, file_patterns
            )
            
            # Create restore staging area
            staging_bucket = f"{self.source_bucket}-restore-staging"
            await self.create_staging_bucket(staging_bucket)
            
            # Restore files to staging
            restored_files = []
            for file_info in files_to_restore:
                try:
                    await self.restore_single_file(
                        file_info["backup_path"], 
                        f"staging/{file_info['original_path']}"
                    )
                    restored_files.append(file_info)
                except Exception as e:
                    print(f"Failed to restore {file_info['original_path']}: {str(e)}")
            
            print(f"✅ File restore completed: {len(restored_files)} files restored to staging")
            
            # Provide verification report
            return {
                "restore_session_id": f"restore_{datetime.utcnow().strftime('%Y%m%d_%H%M%S')}",
                "restore_point": restore_point,
                "backup_session_used": backup_session["backup_session_id"],
                "files_restored": len(restored_files),
                "staging_location": staging_bucket,
                "restored_files": restored_files
            }
            
        except Exception as e:
            print(f"❌ File restore failed: {str(e)}")
            raise
```

---

## Security Incident Response

### Security Breach Response
```python
# Security Incident Response Automation
class SecurityIncidentResponse:
    def __init__(self):
        self.incident_severity = None
        self.affected_systems = []
        self.containment_actions = []
        self.recovery_plan = []
        
    async def handle_security_incident(self, incident_type: str, severity: str):
        """Orchestrate security incident response"""
        
        incident_id = f"SEC-{datetime.utcnow().strftime('%Y%m%d-%H%M%S')}"
        
        print(f"🚨 Security incident detected: {incident_type} (Severity: {severity})")
        print(f"Incident ID: {incident_id}")
        
        # Immediate containment
        await self.immediate_containment(incident_type, severity)
        
        # Assessment and investigation
        incident_details = await self.assess_incident(incident_type)
        
        # Customer and regulatory notification
        await self.handle_notifications(incident_details, severity)
        
        # Recovery and hardening
        await self.execute_recovery_plan(incident_details)
        
        # Post-incident analysis
        await self.conduct_post_incident_review(incident_id, incident_details)
        
        return incident_id
    
    async def immediate_containment(self, incident_type: str, severity: str):
        """Execute immediate containment procedures"""
        
        if incident_type == "data_breach":
            # Isolate affected systems
            await self.isolate_compromised_systems()
            
            # Disable compromised accounts
            await self.disable_suspicious_accounts()
            
            # Block suspicious IP addresses
            await self.update_firewall_rules()
            
            # Preserve forensic evidence
            await self.create_system_snapshots()
            
        elif incident_type == "unauthorized_access":
            # Force password reset for all users
            await self.force_password_reset()
            
            # Revoke all API tokens
            await self.revoke_api_tokens()
            
            # Enable additional authentication
            await self.enable_emergency_mfa()
            
        elif incident_type == "malware_detected":
            # Quarantine affected systems
            await self.quarantine_systems()
            
            # Run malware scans
            await self.run_comprehensive_malware_scan()
            
            # Block malicious domains
            await self.update_dns_filters()
    
    async def handle_lgpd_breach_notification(self, incident_details: dict):
        """Handle LGPD-compliant breach notification"""
        
        # Assess if ANPD notification is required
        anpd_notification_required = await self.assess_anpd_notification_requirement(
            incident_details
        )
        
        if anpd_notification_required:
            # Prepare ANPD notification within 72 hours
            anpd_report = await self.prepare_anpd_breach_report(incident_details)
            
            # Submit to ANPD
            await self.submit_anpd_notification(anpd_report)
            
        # Assess if customer notification is required
        customer_notification_required = await self.assess_customer_notification_requirement(
            incident_details
        )
        
        if customer_notification_required:
            # Notify affected customers
            affected_customers = incident_details.get("affected_customers", [])
            
            for customer_id in affected_customers:
                await self.send_customer_breach_notification(customer_id, incident_details)
```

### Access Control Procedures
```bash
#!/bin/bash
# Access Control Management Procedures

# Emergency Access Revocation
emergency_access_lockdown() {
    echo "🔒 Initiating emergency access lockdown"
    
    # Disable all non-essential user accounts
    for user in $(kubectl get serviceaccounts -o name | grep -v default | grep -v system); do
        kubectl patch $user -p '{"metadata":{"labels":{"status":"disabled"}}}'
    done
    
    # Revoke all API keys
    python3 << EOF
import redis
r = redis.Redis(host='$REDIS_HOST', port=6379, db=0)
api_keys = r.keys('api_key:*')
for key in api_keys:
    r.delete(key)
print(f"Revoked {len(api_keys)} API keys")
EOF
    
    # Force logout all web sessions
    python3 << EOF
import redis
r = redis.Redis(host='$REDIS_HOST', port=6379, db=0)
sessions = r.keys('session:*')
for session in sessions:
    r.delete(session)
print(f"Terminated {len(sessions)} active sessions")
EOF
    
    # Update firewall to block external access
    gcloud compute firewall-rules update default-allow-https \
        --source-ranges=10.0.0.0/8,192.168.0.0/16 \
        --description="Emergency lockdown - internal access only"
    
    echo "✅ Emergency access lockdown completed"
}

# Access Recovery Procedures
restore_normal_access() {
    echo "🔓 Restoring normal access controls"
    
    # Re-enable essential user accounts
    kubectl get serviceaccounts -l status=disabled -o name | while read account; do
        kubectl patch $account -p '{"metadata":{"labels":{"status":"active"}}}'
    done
    
    # Restore firewall rules
    gcloud compute firewall-rules update default-allow-https \
        --source-ranges=0.0.0.0/0 \
        --description="Normal HTTPS access"
    
    # Send access restoration notification
    curl -X POST -H 'Content-type: application/json' \
        --data '{"text":"🔓 Normal access controls restored after security incident"}' \
        $SLACK_SECURITY_WEBHOOK
    
    echo "✅ Normal access controls restored"
}
```

---

## Performance Optimization Playbooks

### Database Performance Tuning
```sql
-- Database Performance Analysis and Optimization

-- Identify slow queries
CREATE OR REPLACE FUNCTION analyze_slow_queries()
RETURNS TABLE(
    query_text text,
    mean_exec_time numeric,
    calls bigint,
    total_exec_time numeric
) AS $$
BEGIN
    RETURN QUERY
    SELECT 
        pg_stat_statements.query,
        ROUND(pg_stat_statements.mean_exec_time::numeric, 2) as mean_time,
        pg_stat_statements.calls,
        ROUND(pg_stat_statements.total_exec_time::numeric, 2) as total_time
    FROM pg_stat_statements
    WHERE pg_stat_statements.mean_exec_time > 100  -- Queries taking > 100ms
    ORDER BY pg_stat_statements.mean_exec_time DESC
    LIMIT 20;
END;
$$ LANGUAGE plpgsql;

-- Analyze table bloat
CREATE OR REPLACE FUNCTION analyze_table_bloat()
RETURNS TABLE(
    schema_name name,
    table_name name,
    bloat_percentage numeric,
    bloat_size text
) AS $$
BEGIN
    RETURN QUERY
    WITH table_stats AS (
        SELECT
            schemaname,
            tablename,
            pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) as size,
            pg_stat_user_tables.n_dead_tup,
            pg_stat_user_tables.n_live_tup,
            CASE 
                WHEN pg_stat_user_tables.n_live_tup > 0 
                THEN (pg_stat_user_tables.n_dead_tup::float / pg_stat_user_tables.n_live_tup::float) * 100 
                ELSE 0 
            END as bloat_pct
        FROM pg_stat_user_tables
        WHERE schemaname NOT IN ('information_schema', 'pg_catalog')
    )
    SELECT 
        table_stats.schemaname,
        table_stats.tablename,
        ROUND(table_stats.bloat_pct, 2),
        table_stats.size
    FROM table_stats
    WHERE table_stats.bloat_pct > 20  -- Tables with >20% bloat
    ORDER BY table_stats.bloat_pct DESC;
END;
$$ LANGUAGE plpgsql;

-- Automated index optimization
CREATE OR REPLACE FUNCTION optimize_database_indexes()
RETURNS void AS $$
DECLARE
    rec RECORD;
BEGIN
    -- Rebuild fragmented indexes
    FOR rec IN (
        SELECT schemaname, tablename, indexname
        FROM pg_stat_user_indexes
        WHERE idx_scan < 100 AND schemaname = 'public'
    ) LOOP
        EXECUTE format('REINDEX INDEX %I.%I', rec.schemaname, rec.indexname);
    END LOOP;
    
    -- Update table statistics
    ANALYZE;
    
    -- Log optimization completion
    INSERT INTO maintenance_log (operation, completed_at, details)
    VALUES ('index_optimization', NOW(), 'Automated index optimization completed');
END;
$$ LANGUAGE plpgsql;
```

### Application Performance Tuning
```python
# Application Performance Monitoring and Optimization
import asyncio
import aioredis
from datetime import datetime, timedelta

class PerformanceOptimizer:
    def __init__(self):
        self.redis_client = None
        self.performance_metrics = {}
        self.optimization_thresholds = {
            "response_time": 2.0,  # seconds
            "memory_usage": 85,    # percentage
            "cpu_usage": 80,       # percentage
            "cache_hit_ratio": 0.8 # 80%
        }
    
    async def run_performance_optimization(self):
        """Run comprehensive performance optimization"""
        
        print("🚀 Starting performance optimization routine")
        
        # Analyze current performance
        performance_analysis = await self.analyze_system_performance()
        
        # Optimize based on findings
        optimizations_applied = []
        
        if performance_analysis["response_time"] > self.optimization_thresholds["response_time"]:
            await self.optimize_response_times()
            optimizations_applied.append("response_time_optimization")
        
        if performance_analysis["cache_hit_ratio"] < self.optimization_thresholds["cache_hit_ratio"]:
            await self.optimize_caching_strategy()
            optimizations_applied.append("cache_optimization")
        
        if performance_analysis["memory_usage"] > self.optimization_thresholds["memory_usage"]:
            await self.optimize_memory_usage()
            optimizations_applied.append("memory_optimization")
        
        # Apply AI model optimizations
        await self.optimize_ai_model_routing()
        optimizations_applied.append("ai_model_routing_optimization")
        
        # Database query optimization
        await self.optimize_database_queries()
        optimizations_applied.append("database_optimization")
        
        print(f"✅ Performance optimization completed. Applied: {', '.join(optimizations_applied)}")
        
        return {
            "optimization_timestamp": datetime.utcnow(),
            "optimizations_applied": optimizations_applied,
            "performance_improvement_expected": "15-30%"
        }
    
    async def optimize_ai_model_routing(self):
        """Optimize AI model routing for cost and performance"""
        
        # Analyze recent AI usage patterns
        usage_patterns = await self.analyze_ai_usage_patterns()
        
        # Identify opportunities to use cheaper models
        downgrade_opportunities = []
        for task_type, stats in usage_patterns.items():
            if stats["avg_quality_score"] > 0.85 and stats["current_model"] != "gemini-2.5-flash":
                downgrade_opportunities.append({
                    "task_type": task_type,
                    "current_model": stats["current_model"],
                    "recommended_model": "gemini-2.5-flash",
                    "potential_savings": stats["monthly_requests"] * 0.049  # $0.05 -> $0.001
                })
        
        # Apply routing optimizations
        for opportunity in downgrade_opportunities:
            await self.update_model_routing(
                opportunity["task_type"],
                opportunity["recommended_model"]
            )
        
        print(f"💰 AI cost optimization: {len(downgrade_opportunities)} routing changes applied")
        
        return downgrade_opportunities
```

---

## Deployment & Rollback Procedures

### Zero-Downtime Deployment
```bash
#!/bin/bash
# Zero-Downtime Deployment Script

DEPLOYMENT_ENV="${1:-production}"
IMAGE_TAG="${2:-latest}"
ROLLBACK_TAG="${3:-}"

echo "🚀 Starting zero-downtime deployment to $DEPLOYMENT_ENV"
echo "Image tag: $IMAGE_TAG"

# Pre-deployment validation
validate_deployment() {
    echo "Validating deployment readiness..."
    
    # Check if new image exists
    if ! docker manifest inspect "gcr.io/aidepartments/$IMAGE_TAG" > /dev/null 2>&1; then
        echo "❌ Image $IMAGE_TAG not found"
        exit 1
    fi
    
    # Run automated tests against staging
    npm run test:e2e:staging
    if [ $? -ne 0 ]; then
        echo "❌ Pre-deployment tests failed"
        exit 1
    fi
    
    # Check database migrations
    python manage.py migrate --dry-run --check
    if [ $? -ne 0 ]; then
        echo "❌ Database migration issues detected"
        exit 1
    fi
    
    echo "✅ Pre-deployment validation passed"
}

# Blue-Green Deployment
perform_blue_green_deployment() {
    echo "Executing blue-green deployment..."
    
    # Identify current active environment
    CURRENT_ENV=$(kubectl get service api-service -o jsonpath='{.spec.selector.version}')
    NEW_ENV=$([ "$CURRENT_ENV" = "blue" ] && echo "green" || echo "blue")
    
    echo "Current environment: $CURRENT_ENV"
    echo "Deploying to: $NEW_ENV"
    
    # Deploy to inactive environment
    kubectl set image deployment/api-service-$NEW_ENV \
        api-service=gcr.io/aidepartments/api-service:$IMAGE_TAG
    
    kubectl set image deployment/worker-service-$NEW_ENV \
        worker-service=gcr.io/aidepartments/worker-service:$IMAGE_TAG
    
    # Wait for rollout completion
    kubectl rollout status deployment/api-service-$NEW_ENV --timeout=300s
    kubectl rollout status deployment/worker-service-$NEW_ENV --timeout=300s
    
    # Health check new environment
    NEW_ENDPOINT="https://api-$NEW_ENV.aidepartments.com.br/health"
    
    for i in {1..30}; do
        if curl -f -s "$NEW_ENDPOINT" > /dev/null; then
            echo "✅ New environment health check passed"
            break
        fi
        echo "Waiting for new environment to be ready... ($i/30)"
        sleep 10
    done
    
    # Run smoke tests
    run_smoke_tests "$NEW_ENV"
    if [ $? -ne 0 ]; then
        echo "❌ Smoke tests failed"
        rollback_deployment "$CURRENT_ENV"
        exit 1
    fi
    
    # Switch traffic to new environment
    kubectl patch service api-service -p '{"spec":{"selector":{"version":"'$NEW_ENV'"}}}'
    
    echo "✅ Traffic switched to $NEW_ENV environment"
    
    # Monitor for 5 minutes
    monitor_deployment_health 300
    
    if [ $? -eq 0 ]; then
        echo "✅ Deployment successful"
        cleanup_old_environment "$CURRENT_ENV"
    else
        echo "❌ Deployment health check failed"
        rollback_deployment "$CURRENT_ENV"
        exit 1
    fi
}

# Automated Rollback
rollback_deployment() {
    ROLLBACK_ENV="$1"
    
    echo "🔄 Rolling back to $ROLLBACK_ENV environment"
    
    # Switch traffic back
    kubectl patch service api-service -p '{"spec":{"selector":{"version":"'$ROLLBACK_ENV'"}}}'
    
    # Verify rollback health
    ROLLBACK_ENDPOINT="https://api.aidepartments.com.br/health"
    
    for i in {1..10}; do
        if curl -f -s "$ROLLBACK_ENDPOINT" > /dev/null; then
            echo "✅ Rollback health check passed"
            break
        fi
        echo "Waiting for rollback to stabilize... ($i/10)"
        sleep 5
    done
    
    # Send rollback notification
    curl -X POST -H 'Content-type: application/json' \
        --data "{\"text\":\"🔄 Deployment rolled back to $ROLLBACK_ENV environment\"}" \
        $SLACK_DEPLOY_WEBHOOK
    
    echo "✅ Rollback completed"
}

# Execute deployment
validate_deployment
perform_blue_green_deployment
```

---

## Cost Optimization Runbooks

### Automated Cost Control
```python
# Automated Cost Optimization System
class CostOptimizationEngine:
    def __init__(self):
        self.cost_thresholds = {
            "daily_budget": 50.0,      # USD
            "monthly_budget": 1500.0,  # USD
            "ai_cost_ratio": 0.6,      # 60% of total costs
            "infrastructure_ratio": 0.4 # 40% of total costs
        }
        
    async def run_daily_cost_optimization(self):
        """Run daily cost optimization routine"""
        
        print("💰 Starting daily cost optimization")
        
        # Get current cost metrics
        cost_metrics = await self.get_current_cost_metrics()
        
        optimizations_applied = []
        
        # AI model cost optimization
        if cost_metrics["ai_costs"]["daily"] > self.cost_thresholds["daily_budget"] * 0.6:
            ai_savings = await self.optimize_ai_model_usage()
            optimizations_applied.extend(ai_savings)
        
        # Infrastructure cost optimization
        if cost_metrics["infrastructure_costs"]["daily"] > self.cost_thresholds["daily_budget"] * 0.4:
            infra_savings = await self.optimize_infrastructure_costs()
            optimizations_applied.extend(infra_savings)
        
        # Storage cost optimization
        storage_savings = await self.optimize_storage_costs()
        optimizations_applied.extend(storage_savings)
        
        # Generate cost optimization report
        optimization_report = {
            "date": datetime.utcnow().date(),
            "total_optimizations": len(optimizations_applied),
            "estimated_monthly_savings": sum(opt.get("monthly_savings", 0) for opt in optimizations_applied),
            "optimizations": optimizations_applied
        }
        
        # Send report to stakeholders
        await self.send_cost_optimization_report(optimization_report)
        
        print(f"✅ Cost optimization completed: ${optimization_report['estimated_monthly_savings']:.2f}/month savings")
        
        return optimization_report
    
    async def optimize_ai_model_usage(self):
        """Optimize AI model usage for cost efficiency"""
        
        optimizations = []
        
        # Analyze model usage patterns
        model_usage = await self.analyze_model_usage_patterns()
        
        # Identify tasks using expensive models unnecessarily
        for task_type, usage_data in model_usage.items():
            if usage_data["current_model"] == "gpt-4-turbo" and usage_data["quality_score"] < 0.9:
                # Downgrade to GPT-3.5 Turbo
                await self.update_model_routing(task_type, "gpt-3.5-turbo")
                monthly_savings = usage_data["monthly_requests"] * 0.047  # $0.05 - $0.003
                optimizations.append({
                    "type": "model_downgrade",
                    "task_type": task_type,
                    "from_model": "gpt-4-turbo",
                    "to_model": "gpt-3.5-turbo",
                    "monthly_savings": monthly_savings
                })
            
            elif usage_data["current_model"] == "gpt-3.5-turbo" and usage_data["quality_score"] > 0.75:
                # Downgrade to Gemini 2.5 Flash
                await self.update_model_routing(task_type, "gemini-2.5-flash")
                monthly_savings = usage_data["monthly_requests"] * 0.002  # $0.003 - $0.001
                optimizations.append({
                    "type": "model_downgrade",
                    "task_type": task_type,
                    "from_model": "gpt-3.5-turbo",
                    "to_model": "gemini-2.5-flash",
                    "monthly_savings": monthly_savings
                })
        
        return optimizations
    
    async def implement_emergency_cost_controls(self):
        """Implement emergency cost controls when budget is exceeded"""
        
        print("🚨 Implementing emergency cost controls")
        
        # Switch all tasks to most cost-efficient model
        await self.route_all_tasks_to_gemini_flash()
        
        # Reduce infrastructure resources
        await self.scale_down_infrastructure()
        
        # Pause non-essential background jobs
        await self.pause_non_essential_jobs()
        
        # Implement request rate limiting
        await self.implement_stricter_rate_limits()
        
        # Send emergency notification
        await self.send_emergency_cost_notification()
        
        print("✅ Emergency cost controls implemented")
```

---

**Document Status:** Comprehensive operational runbooks complete with incident response, monitoring, backup/recovery, security procedures, performance optimization, deployment protocols, and cost management. Ready for 24/7 SRE operations.**