# VPC Flow Logs is not enabled for VPC

In the code example VPC Flow Logs is not enabled for VPC

```terraform
resource "google_compute_subnetwork" "insecure_example" {
  name          = "test-subnetwork"
  ip_cidr_range = "10.2.0.0/16"
  region        = "us-central1"
  network       = google_compute_network.custom-test.id
  secondary_ip_range {
    range_name    = "tf-test-secondary-range-update1"
    ip_cidr_range = "192.168.10.0/24"
  }
}

resource "google_compute_network" "custom-test" {
  name                    = "test-network"
  auto_create_subnetworks = false
}
```

# Why it's vulnerable?

VPC flow logs record information about all traffic, which is a vital tool in reviewing anomalous traffic. It may lead to limited auditing capability and awareness

# How to fix?

Enable VPC flow logs

```terraform
resource "google_compute_subnetwork" "secure_example" {
  name          = "test-subnetwork"
  ip_cidr_range = "10.2.0.0/16"
  region        = "us-central1"
  network       = google_compute_network.custom-test.id
  secondary_ip_range {
    range_name    = "tf-test-secondary-range-update1"
    ip_cidr_range = "192.168.10.0/24"
  }
  log_config {
    aggregation_interval = "INTERVAL_10_MIN"
    flow_sampling        = 0.5
    metadata             = "INCLUDE_ALL_METADATA"
  }
}

resource "google_compute_network" "custom-test" {
  name                    = "test-network"
  auto_create_subnetworks = false
}
```
