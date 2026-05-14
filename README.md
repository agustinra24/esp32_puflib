# esp32_puflib

ESP-IDF component for SRAM-PUF experiments on ESP32-class microcontrollers.
The component measures startup SRAM behavior, stores helper data, and exposes a
PUF response API for enrollment and reconstruction workflows.

This component is used as a baseline in `did-puf-framework`. Treat its output as
experimental identity material until the complete enrollment, helper handling,
and lifecycle model have been validated for the target deployment.

## Integration

Add this directory to the ESP-IDF component search path:

```cmake
set(EXTRA_COMPONENT_DIRS path/to/esp32_puflib)
```

Include the public header from application code:

```c
#include "puflib.h"
```

## Partition Requirement

The component stores helper data in NVS during enrollment. The application must
provide a partition table with enough NVS space for the target helper data and
enrollment metadata.

Review the consuming firmware partition table before changing PUF profile,
sample count, or helper format.

## Minimal Example

```c
#include "puflib.h"

void app_main(void)
{
    puflib_init();

    if (!is_puf_enrolled()) {
        enroll_puf();
        get_puf_response_reset();
        return;
    }

    puf_response_t response = {0};
    if (get_puf_response(&response) == ESP_OK) {
        /* Use response.bytes within the application trust boundary. */
    }
}
```

## Security Notes

- PUF responses, helper data, and derived keys are sensitive identity material.
- Enrollment must be bound to a lifecycle policy in the consuming system.
- Helper data handling should be reviewed before treating it as public.
- Physical attacks, side channels, and re-enrollment attacks are not solved by
  this component alone.

## License

See `LICENSE`.
