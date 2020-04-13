# LAUNCH IN TERMINAL FROM XCODE

1. Create new scheme. Edit it, **Run** > **Info** > **Executable**, change it to Terminal and uncheck **Debug executable**. Not debuggable in this mode.
2. Go to **Arguments** tab and add `${BUILT_PRODUCTS_DIR}/${FULL_PRODUCT_NAME}`. Add other args as needed.
