sed -i "s/PDS_JWT_SECRET=/PDS_JWT_SECRET=$(openssl rand --hex 16)/" pds.env
sed -i "s/PDS_ADMIN_PASSWORD=/PDS_ADMIN_PASSWORD=$(openssl rand --hex 16)/" pds.env
sed -i "s/PDS_PLC_ROTATION_KEY_K256_PRIVATE_KEY_HEX=/PDS_PLC_ROTATION_KEY_K256_PRIVATE_KEY_HEX=$(openssl ecparam --name secp256k1 --genkey --noout --outform DER | tail --bytes=+8 | head --bytes=32 | xxd --plain --cols 32)/" pds.env